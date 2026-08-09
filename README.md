# Amiga Pi to HDMI

The **Amiga Pi to HDMI** board is an open-source video, audio, and FlashFloppy interface for big-box Amiga computers. It plugs into the Amiga Video Slot and uses Raspberry Pi running [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI) with several additional functions on a single board.

The primary function is to capture the Amiga's native video and with RGBtoHDMI, produce HDMI video on a connector at the rear of the Amiga.

The current hardware integrates:

- **Raspberry Pi video processing** using the RGBtoHDMI software.
- **HD Audio capture**, allowing Amiga audio to be digitised by the board.
- **STM32F103 "Blue Pill" FlashFloppy OSD controller**, providing an on-screen display for FlashFloppy and allowing the Amiga keyboard to control FlashFloppy.
- **HDMI switching**, allowing the Pi-generated video to be switched with a second HDMI video source connected internally.
- **Rear-facing HDMI output**, so the Amiga can be connected to a normal HDMI display without an external video adapter.
- **USB-C console for the STM32** providing easy access to load firmware and configure the FlashFloppy OSD

The board is designed for the A2000 and A3000 Video Slot. It also works with the A4000, but is limited to 12-bit capture of the AGA 24-bit video, and only with OCS video modes.

## Hardware revisions

The hardware has evolved substantially since the original design. The current revision history is documented in [Hardware Revisions](doc/hw_revisions.md).

In particular, **Rev6** introduced the integrated STM32F103 FlashFloppy OSD controller, HD Audio circuit, and HDMI switch. **Rev7.1** added a CH340 USB-to-serial interface. and **Rev7.2** changes the secondary HDMI input from a male connector to a female connector.

## System architecture

At a high level, the board consists of four functional sections:

```text
                    Amiga Video Slot             Gotek        Amiga
                           |                   FlashFloppy   Keyboard
             +-------------+----------+            |             |
             |                        |            |             |
     Digital Amiga video         Amiga audio       |             |
             |                        |          STM32 FlashFloppy OSD
             v                        v            |       |
    Video Capture (CPLD)           HD Audio        |       |
             |                   Capture (PCM)    OSD    Switch
             v                        |            |     Control
      Raspberry Pi Zero <-------------+            |       |
       + RGBtoHDMI      <--------------------------+       |
             |                                             |
             v                                             |
        HDMI output                                        |
             |      +--------------------------------------+
             v     /
        HDMI switch <------ Secondary HDMI input
             |
             v
      Rear HDMI connector
```

The STM32F103 is connected to the FlashFloppy Gotek and to the Amiga keyboard. It generates the FlashFloppy OSD data which is incorporated into the Pi's video output.

## Documentation

| Document | Description |
| --- | --- |
| [Hardware Revisions](doc/hw_revisions.md) | Hardware revision history and the major differences between revisions. |
| [Hardware Build Guide](doc/hardware_build.md) | Building the PCB, installing components, programming the programmable devices, and installing the Raspberry Pi. |
| [Hardware Configuration](doc/hardware_configuration.md) | Configuration of the Raspberry Pi, video capture, HDMI routing, audio, and STM32 interfaces. |
| [FlashFloppy OSD](doc/flashfloppy_osd.md) | Building and installing the custom STM32F103 FF OSD firmware and connecting it to FlashFloppy. |
| [Amiga Installation](doc/amiga_installation.md) | Installing the completed board in an A2000/A3000 and connecting the peripheral wiring. |
| [Using the Board](doc/usage.md) | Normal operation, HDMI switching, FlashFloppy OSD, keyboard control, and audio. |
| [Troubleshooting](doc/troubleshooting.md) | Diagnostic information for common installation and operating problems. |

## Raspberry Pi software

The Raspberry Pi runs the multi-platform vintage-hardware [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI) software.

RGBtoHDMI provides the real-time capture and display processing. The board supplies the Amiga-specific video capture hardware and routes the resulting HDMI signal through the onboard HDMI switch.

Consult the RGBtoHDMI documentation for Raspberry Pi software installation, SD-card preparation, configuration files, profiles, and display settings.

## FlashFloppy OSD software

The STM32F103 firmware used by this board is maintained separately:

[cdhooper/flashfloppy-osd-avhdmi](https://github.com/cdhooper/flashfloppy-osd-avhdmi)

This is a board-specific fork of [keirf/flashfloppy-osd](https://github.com/keirf/flashfloppy-osd). It provides:

- FlashFloppy OSD output on the Amiga display.
- Optional Amiga keyboard control of FlashFloppy.
- The LCD I2C interface expected by FlashFloppy-compatible hardware.

See [FlashFloppy OSD](doc/flashfloppy_osd.md) for board-specific installation instructions.

The upstream FF OSD project documents the general FlashFloppy connection scheme and the required I2C pull-ups. The board-specific firmware and schematic should be treated as authoritative for this hardware.

## FlashFloppy configuration

The board does not replace FlashFloppy itself. A compatible Gotek must still be running FlashFloppy firmware.

For general FlashFloppy setup, see the [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup) documentation.

For FF OSD connections, see the [FlashFloppy OSD Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections) documentation.

## Building the hardware

This repository contains the hardware design files. The exact bill of materials, component values, footprints, assembly drawings, and programmable-device files are revision-specific.

Before ordering or assembling a board:

1. Identify the PCB revision.
2. Read [Hardware Revisions](doc/hw_revisions.md).
3. Use the schematic and PCB files for that exact revision.
4. Use the revision-specific BOM rather than assuming that a component from another revision is interchangeable.
5. Complete programming and configuration before installing the board in an Amiga.

See [Hardware Build Guide](doc/hardware_build.md).

## Installing in an Amiga

The board is intended to plug directly into the Video Slot. The Raspberry Pi and HDMI hardware are mounted on the board so that the HDMI connection can be made from the rear of the computer.

**Power off the Amiga before installing or removing the board.**

See [Hardware Revisions](doc/hw_revisions.md) and the assembly documentation before fitting the Pi.

See [Amiga Installation](doc/amiga_installation.md).

## A4000 limitations

The board can be used with an A4000, but the current design does not provide the full AGA 24-bit video path. The documented limitation is **12-bit video and OCS resolution screen modes**. Only OCS video modes are supported by RGBtoHDMI.

## Design background

The Rev1 and Rev2 designs were based on the [Amiga-Digital-Video](https://github.com/c0pperdragon/Amiga-Digital-Video) Denise-socket design.

Rev3 and Rev4 moved to a CPLD-based video capture architecture, based loosely on the [LinuxJedi AmigaRGBtoHDMI](https://github.com/LinuxJedi/AmigaRGBtoHDMI) work. The CPLD samples CSYNC to capture video timing rather than reconstructing the 7 MHz clock, improving capture reliability.

All revisions use the [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI) software on the Raspberry Pi.

## Open-source hardware

This project is intended to be buildable from the published hardware design files. When reproducing a board, use the schematic, PCB layout, BOM, and revision documentation together. Do not mix components or configuration information between revisions without checking the electrical design.

## License

See the license files included in this repository and in the component projects linked above.
