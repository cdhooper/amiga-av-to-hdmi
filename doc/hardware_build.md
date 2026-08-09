# Hardware Build Guide

This guide describes the recommended build sequence for an Amiga Pi to HDMI board.

Because the PCB has multiple hardware revisions, **the schematic and BOM for the exact PCB revision being built are authoritative**. Component values and assembly details must not be copied between revisions without checking the design files.

## 1. Identify the PCB revision

Before starting assembly, identify the revision printed on the PCB.

Read [Hardware Revisions](hw_revisions.md) and collect the following revision-specific files:

- Schematic.
- PCB layout.
- BOM.
- Pick-and-place data, if applicable.
- Assembly drawing.
- CPLD programming file, if applicable.
- STM32 firmware.

The Rev4 version is now legacy. Use the Rev7.1 or Rev7.2 version.

## 2. PCB assembly

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

### Inspection checklist

- Inspect all solder joints.
- Check for solder bridges on fine-pitch ICs.
- Check the power rails for shorts to ground.
- Verify polarized components (diodes / LED).
- Verify the STM32F103, XC9572XL, PCM1808, CH340, and 74AUP1G157 orientation.

## 3. No logic needs to be programmed before installation in Amiga.

The board contains a Xilinx XC9572XL CPLD and STM32F103.
Both of these devices will store logic / firmware for proper operation.
Both of these will be programmed after installation in the Amiga.

## 4. Install the Raspberry Pi

Install the Raspberry Pi only after confirming the orientation for the PCB revision.

Incorrect orientation can cause electrical or mechanical damage.
The Raspberry Pi must be installed face-down on current revision boards.
The Rev7 design allows HDMI from the Pi to be captured without using
a cable. Instead, a separate tiny [adapter board](../rev6_adapter_4/)
is required. This adapter board has two connectors (HDMI male and male
2x8 Dupont male header). It should be plugged in to the Raspberry Pi,
and then both the Pi and the adapter will be plugged in to the
Amiga **AV to HDMI board** at the same time.

### Pi Zero or Pi Zero 2

The board is designed around a Raspberry Pi Zero for RGBtoHDMI processing. The Pi connects to the board through the GPIO connector and supplies the processing platform for the RGBtoHDMI software. There is no major advantage in choosing the Pi Zero 2 over the Pi Zero 1.3.

Use the mounting position specified for the PCB revision.

## 5. Install the HDMI hardware

The board routes the Raspberry Pi HDMI signal through the onboard HDMI switch.

The switched output is connected to the rear-facing HDMI connector.

The secondary HDMI input is intended for a second internal HDMI source.

On Rev7.2 the secondary HDMI input uses a female HDMI connector. Earlier revisions use a male connector for the secondary HDMI input.

## 6. Install the FlashFloppy OSD after the board is powered in the Amiga

You must install a jumper at each of J1, J2, and J3.
If using the FlashFloppy OSD, you should install J1, J2, and J3 in the
2-3 position. If you are not using FlashFloppy OSD, you may install
the J1, J2, and J3 jumpers in either position, but they must be consistent.
The 1-2 position is detected automatically, and allows the Pi to sample
audio without (automatic) overclocking.

Rev7 and later provide a CH340 USB-to-serial interface. You can connect
your PC to the USB-C port on the board for programming and configuration.
If running Windows, You may need to install a CH340 driver on your computer.

Install the STM32 FlashFloppy OSD firmware after the board is powered in
the Amiga. The STM32 firmware is maintained in the separate:

[cdhooper/flashfloppy-osd-avhdmi](https://github.com/cdhooper/flashfloppy-osd-avhdmi)

repository.

See [FlashFloppy OSD](flashfloppy_osd.md) after installing the board in the Amiga.

## 7. Audio circuit

Rev6 and later contain an HD Audio circuit for capturing Amiga audio.

Inspect the audio section carefully during assembly because the audio path contains analogue circuitry where soldering faults may not be obvious from a digital continuity test.

For audio capture, the Raspberry Pi will need to have a current version
of the RGBtoHDMI software installed. You may need to enable Audio Capture
in the RGBtoHDMI setup menu.

J1, J2, and J3 must have jumpers installed for audio to function.
See the FlashFloppy OSD section above.


## 8. Installation and test

Once the board has been configured, install it in the Amiga using [Amiga Installation](amiga_installation.md).

Do not install or remove the board while the Amiga is powered.
