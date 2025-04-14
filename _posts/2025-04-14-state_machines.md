---
layout: default
author: KI3P
title: A better way to use state machines
use_math: true
---

# Motivation

One of my amateur radio pursuits is helping to maintain the [source code](https://github.com/KI3P/T41-V12-SDT) for the T41 software defined transceiver. This code base is remarkable: a fully featured open source code for a radio transceiver. The code was originally derived from the [Teensy Convolution SDR](https://github.com/DD4WH/Teensy-ConvolutionSDR/blob/master/Teensy_Convolution_SDR.ino) code, with many many contributors since then (primarily Jack Purdum W8TEE and Al Peter AC8GY) performing code refactoring, adding features, fixing bugs, etc. The code has evolved to support a host of different hardware versions over the years: V10, V11, V12, and combinations of boards from each.

Any piece of code that receives so much attention from so many people will grow a bit ragged over time. Particularly so if the hardware target it's written for is also evolving. All this adds up to the outcome that understanding and debugging the T41 code is not for the faint of heart, particularly since the Teensy 4.1 microcontroller board does not have a debug interface.

I've been thinking about doing a major rewrite of the T41 code for a while. This rewrite has to solve several problems:

* Hardware-dependent code is scattered throughout the code base. Changing the hardware design means tracking down all this code. This is particularly acute for the display code.
* The code is impossible to test off-board, so debugging relies on scattering `printf` statements throughout the code, flashing it to the radio, then watching the serial port to find the bugs.
* Because of the aforementioned scattering of hardware-dependent code, it's easy to make a mistake and put your hardware in an undesirable configuration state.

# The solution

I think I can solve all three of these problems by taking the following steps:

1. Define a hardware abstraction layer for the radio hardware and a strict set of valid configuration states for the hardware.
2. Use state machines to move hardware between states in response to button presses, keyers, etc.
3. Use a testing framework like [Google Test](https://google.github.io/googletest/) to automate testing of all the hardware-independent code. 

## Configuration states

I'm working my way through the hardware, examining the function of each configurable/controllable part of the hardware, and defining a set of valid states. They will look something like this:

![](/assets/images/better_way_state_machines/RF_board_states.png)

## State machines

Then, I'm documenting various state machines that show how the radio transitions between states as various buttons are pressed or other events happen. I'm drawing them in little state diagrams like this.

![](/assets/images/better_way_state_machines/state_transitions_sketch.png)

The next step would be to write some C code to define these states and switch between them. This is good, but it has pitfalls: what if we change the control flow? The C code and the state diagram need to be manually updated and kept in sync. This is hard to keep doing over the long term, leading to code that is hard to understand because it doesn't match the documentation.

# A better way

I thought that surely someone had solved this problem and found a way to convert state machine drawings into code. If code can be automatically generated from the state machine drawing, then you only need to update the drawing and re-generate the code.

And indeed, a quick search on the internet showed many such solutions. However, most are expensive and/or proprietary. I would like to keep the T41 software as open source as possible -- this applies to the tooling as much as the code. 

That's when I found [StateSmith](https://github.com/StateSmith/StateSmith). StateSmith allows me to draw the state diagram in [Draw.IO](https://www.drawio.com/) and then generate C code from it. Here's the same state diagram from before, but this time drawn using the StateSmith template and syntax.

![](/assets/images/better_way_state_machines/state_transitions_UML.png)

I can then generate C code using the command line:
```
ss.cli run -h
```

This generates two files: `ModeSm.c` and `ModeSm.h`. These define a state machine that contains all the logic and control flow of the drawing. This extract from my testing code (more on this in a later post) shows how to use it.

```c
#include "ModeSm.h"
// Create a ModeSm state machine and start it (enter the initial state)
ModeSm sm;
ModeSm_start(&sm);

// Check that we're in the expected start state of SSB_RECEIVE
EXPECT_EQ(sm.state_id, ModeSm_StateId_SSB_RECEIVE);

// Send an event that corresponds to the MODE button being pressed
// and confirm that we've switched to the CW_RECEIVE state
ModeSm_dispatch_event(&sm, ModeSm_EventId_MODE);
EXPECT_EQ(sm.state_id, ModeSm_StateId_CW_RECEIVE);

// Send a KEY1_PRESSED event, check that we switched to the
// CW_TRANSMIT_MARK state
ModeSm_dispatch_event(&sm, ModeSm_EventId_KEY1_PRESSED);
EXPECT_EQ(sm.state_id, ModeSm_StateId_CW_TRANSMIT_MARK);

// Send a KEY1_RELEASED event, check that we switched to the
// CW_TRANSMIT_SPACE state
ModeSm_dispatch_event(&sm, ModeSm_EventId_KEY1_RELEASED);
EXPECT_EQ(sm.state_id, ModeSm_StateId_CW_TRANSMIT_SPACE);
```

The state machine expects functions called `ModeSSBReceiveEnter()`, `ModeSSBReceiveExit()`, etc. to be defined somewhere else. This lets me separate all my radio-specific code from the state machine by placing it in these functions.