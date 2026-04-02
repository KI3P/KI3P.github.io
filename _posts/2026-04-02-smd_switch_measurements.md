---
layout: default
author: KI3P
title: Surface Mount RF Switch Measurements
date: 2026/04/02
---

# Introduction

Most commercial surface mount RF switches on the market do not specify how well they work at HF frequencies. In reality, most are likely to work quite well, despite most switches having a minimum input frequency of 10 or 20 MHz. This post characterizes a number of switches available on the market in the SC70-6 / SOT363 footprint between 500 kHz and 100 MHz. I am primarily interested in the insertion loss and isolation of the switches.

# Measurement setup

Individual switches were soldered onto SC70-6 to DIP breakboards available on [Amazon](https://www.amazon.com/dp/B0G1SGFKJH). Photographs showing a close-up view of a soldered part and the full breakout board are shown below.

![An SC70-6 footprint switch soldered into a breakout board](/assets/images/smd_rf_switches/breakout_board.png)

The devices tested here all have the same pinout, shown below. Some devices allow a single control logic signal in addition to complementary logic signals; all devices were tested with complementary control logic signals, i.e., if one control line was driven high, the other was driven low.

![Chip pinout](/assets/images/smd_rf_switches/pinout.png)

I used a measurement jig to enable devices under test to be switched in and out. The control lines were driven at 3.3V by a microcontroller. A picture of the test jig is shown below. A 100 nF capacitor was inserted between the RF pins and the SMA connectors.

![Measurement jig](/assets/images/smd_rf_switches/measurement_jig.jpg)

A calibrated NanoVNA was used to measure the insertion loss between the common RF connection (RFC) and the two output RF connections RF1 and RF2; the RF output not being tested was terminated with a 50 Ohm load.

# Switches under test

| Part # | LCSC | Digikey | Mouser | P1 [dB] | Isolation [dB]* | Single control | Unconnected switch |
|--------|------|---------|--------|------|-------|-----|----|
| AS179-92LF | [$1.36](https://www.lcsc.com/product-detail/C83422.html) | [$1.06](https://www.digikey.com/en/products/detail/skyworks-solutions-inc/AS179-92LF/2052013) | [$1.06](https://www.mouser.com/ProductDetail/Skyworks-Solutions-Inc/AS179-92LF?qs=WMHGlxXAKT9LJZT3OzoLeg%3D%3D) | 30 | -85 | No | Float |
| XA17-G4K | [$0.54](https://www.lcsc.com/product-detail/C513494.html) | NA | NA | 30 | -79 | No | Float |
| TPHWS314 | [$0.14](https://www.lcsc.com/product-detail/C47117617.html) | NA | NA | 30 | -78 | No | Float |
| TPE4259-63 | [$0.13](https://www.lcsc.com/product-detail/C47117600.html) | NA | NA | 31 | -76 | No | Float |
| HX4259-63-ST | [$0.15](https://www.lcsc.com/product-detail/C48031087.html) | NA | NA | 33.5 | -75 | Yes | Ground | 
| PE4259-63 | [$0.62](https://www.lcsc.com/product-detail/C470892.html) | [$0.79](https://www.digikey.com/en/products/detail/psemi/4259-63/2614473?s=N4IgTCBcDaIAoFEAsYCsBOAtANgMwgF0BfIA) | NA | 33.5 | -64 | Yes | Ground |
| PE42421-SCAA-Z | [$1.51](https://www.lcsc.com/product-detail/C470893.html) | [$0.79](https://www.digikey.com/en/products/detail/psemi/PE42421SCAA-Z/3728588?s=N4IgTCBcDaIAoFEAsYUEYDKBhAgjgtAFogC6AvkA) | [$0.79](https://www.mouser.com/ProductDetail/pSemi/PE42421SCAA-Z?qs=iw0hurA%2FaD1TPb5bw9tzqA%3D%3D) | 33.5 | -65 | Yes | Ground |
| MASWSS0115TR-3000 | [$1.97](https://www.lcsc.com/product-detail/C3304186.html) | [$1.32](https://www.digikey.com/en/products/detail/macom-technology-solutions/MASWSS0115TR-3000/4429789) | [$1.32](https://www.mouser.com/ProductDetail/MACOM/MASWSS0115TR-3000?qs=3Wmz%2FrCSAaEflvnPFG8Leg%3D%3D) | 25 | -48 | No | Float |
| MASWSS0179TR-3000** | [$0.48](https://www.lcsc.com/product-detail/C3304462.html) | [$1.06](https://www.digikey.com/en/products/detail/macom-technology-solutions/MASWSS0179TR-3000/4429803) | [$1.05](https://www.mouser.com/ProductDetail/MACOM/MASWSS0179TR-3000?qs=3Wmz%2FrCSAaGQmf%252BaN4qHBg%3D%3D) | 16 | -48 | No | Float |

(NA = Not Available. *At 1MHz. **Different footprint, added for reference. Prices are for minimum order quantity, checked on 2 April 2026).

All the devices continued to perform well below their minimum rated frequency. The amateur radio bands are highlighted greenish in the plots below.

![Insertion loss](/assets/images/smd_rf_switches/RF_insertion_loss.png)

Note that the absolute value of the insertion loss includes the loss of the measurement jig and breakout board and is not representative of the SMD device's insertion loss. However, since all devices shared the same measurement jig and breakout board, they can be compared relative to each other.

![Isolation](/assets/images/smd_rf_switches/RF_isolation.png)

# Conclusions

Almost every switch tested works significantly better than the MASWSS0179 version used in the T41 V12 radio. The optimum parts from a cost and isolation performance perspective are provided in the table below. They are Asian brands not available through Digikey or Mouser. The AS179-92LF part has the best isolation performance and is available from US suppliers, but is also significantly more expensive.

| Part # | LCSC | Digikey | Mouser | P1 [dB] | Isolation [dB] @ 1 MHz |
|--------|------|---------|--------|------|-------|
| TPHWS314 | [$0.14](https://www.lcsc.com/product-detail/C47117617.html) | NA | NA | 30 | -78 |
| TPE4259-63 | [$0.13](https://www.lcsc.com/product-detail/C47117600.html) | NA | NA | 31 | -76 | 
| HX4259-63-ST | [$0.15](https://www.lcsc.com/product-detail/C48031087.html) | NA | NA | 33.5 | -75 |
