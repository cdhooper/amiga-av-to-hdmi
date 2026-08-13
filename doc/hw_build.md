# Hardware Build Guide

This guide describes the recommended build sequence for an Amiga AV to HDMI board and the Pi video adapter.

Because the PCB has multiple hardware revisions, use the relevant schematic and BOM for the exact PCB revision being built. It is recommended that you use the latest revision when building a new board.

1. Have the PCBs built.
2. Identify the PCB revision.
3. Assemble the PCB using the BOM for that specific revision.
4. It is not necessary to program anything, other than the Pi SD card before installation in the Amiga.

## 1. Have the PCBs built

The Rev4 version is now legacy. Use the Rev7.1 or Rev7.2 version.

It is recommended you use the latest revision of the Amiga AV to HDMI and adapter board. These are currently:

[Amiga AV to HDMI](../rev7.2/)

[Pi HDMI Adapter](../rev6_adapter_4/)

If you plan to use automated assembly, this will work well at JLCPCB for the Amiga AV to HDMI. At the time the board was designed, most parts other than the XC9572XL were in stock at JLCPCB.

The Pi HDMI Adapter is another matter. The mini HDMI male is not in stock, and even if it were, JLCPCB can't automatically assemble with that connector. I recommend you manually assemble at least this board.

## 2. Identify the PCB revision

Before starting assembly, identify the revision printed on the PCB.

Read [Hardware Revisions](hw_revisions.md) and collect the following revision-specific files:

- Schematic.
- PCB layout.
- BOM.
- Assembly drawing.

## 3. PCB assembly

Populate the board using the BOM for the exact revision.

A practical assembly order is:

1. Kapton tape over the gold fingers.
2. Components requiring hot air (HDMI Switch, 12.288 MHz oscillator).
   After hot air, HDMI Switch may require additional touch-up with a
   fine tip soldering iron.
3. Larger SMD components (XC9572XL, STM32, PCM1808).
4. USB-C connector.
5. HDMI Connectors.
6. Small SMD ICs (CH340C, 74LVC1G80, 74AUP1G157)
7. Small passive components.
8. Through-hole components, including connector headers.

The Pi HDMI adapter only has three components: the PCB, HDMI male connector, and a male 8x2 2.54mm pitch header. You should build this board in addition to the main Amiga AV to HDMI board. The recommended assembly order is:
1. The mini HDMI connector
2. The 8x2 male header. Be sure to mount this header on the correct side of the board.

### Inspection checklist

- Inspect all solder joints.
- Check for solder bridges on fine-pitch ICs.
- Check the power rails for shorts to ground.
- Verify polarized components (diodes / LED).
- Verify the STM32F103, XC9572XL, PCM1808, CH340, and 74AUP1G157 orientation.

### Audio circuit

Rev6 and later contain an HD Audio circuit for capturing Amiga audio.

Inspect the audio section carefully during assembly because the audio path contains analog circuitry where soldering faults may not be obvious from a digital continuity test.

## 4. No logic needs to be programmed before installation in Amiga.

The board contains a Xilinx XC9572XL CPLD and STM32F103.
Both of these devices will store logic / firmware for proper operation.
Both of these will be programmed after installation in the Amiga.

## You are done with the hardware build.

Follow the [Amiga Installation](amiga_install.md) section for how to assemble and install the hardware in your Amiga.
