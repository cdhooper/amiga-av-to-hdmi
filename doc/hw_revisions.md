# Hardware Revisions

This document records the major hardware changes in the Amiga AV to HDMI board.

The board has gone through several iterations, each improving on the previous.

## Rev1 (2021-02-06)

The Rev1 design is based on the Denise-socket [Amiga-Digital-Video](https://github.com/c0pperdragon/Amiga-Digital-Video) design for OCS and ECS Amigas using a 48-pin DIP Denise.

The original board provided several Raspberry Pi mounting/output options:

1. A Raspberry Pi Zero mini-HDMI connector could be exposed directly through the rear of the computer.
2. A Raspberry Pi could be mounted in an alternate position and connected to a full-size rear HDMI connector with an adapter cable.
3. A full-size Raspberry Pi, such as a Pi 2 B+, could be mounted on the board with its HDMI connector exposed at the rear.

The Rev1 Raspberry Pi mounting arrangements required the Pi to be mounted face-up. Depending on the Pi model and connector arrangement, this could require modification of the Pi's connector arrangement.

## Rev2 (2021-03-22)

Rev2 added:

- ECS Denise support.
- A rear-facing, board-mounted full-size HDMI connector.
- A FlashFloppy OSD display header.
- Timing adjustment to compensate for timing differences between some Amiga motherboards.
- An additional Raspberry Pi mounting option which allowed the Pi to be mounted face-down.

The preferred Rev2 Pi mounting arrangement remained face-up, with the middle Pi connector option providing the alternate orientation.

## Rev3 (2021-05-16)

Rev3 changed the video capture architecture substantially.

The discrete video-capture logic was replaced by a single CPLD. The CPLD captures the Amiga video timing using CSYNC rather than attempting to reconstruct the 7 MHz clock or relying on the CDAC clock.

This eliminates the Denise-type jumper and the Rev2 timing adjustment and makes video capture substantially more tolerant of timing differences between Amiga motherboards.

Additional Rev3 changes:

- HDMI passthrough connectors were moved closer together.
- HDMI traces were length-matched to improve signal integrity.
- Three rear-facing buttons became an option instead of the single-button Rev2 arrangement.
- The three Raspberry Pi connector positions were rotated/flipped.
- The default Pi mounting orientation became face-down.
- The middle Pi connector position retained a face-up mounting option.

The face-down orientation is useful because most Raspberry Pi boards have their GPIO headers installed on the top side.

## Rev4 (2021-07-18)

Rev4 fixed several Rev3 errata and became the stable released version for several years.

In all models pay particular attention to Raspberry Pi orientation. The microSD slot must face **away from the Amiga card edge**. Incorrect orientation can result in electrical or mechanical damage.

## Rev5 (2026-04-03)

Rev5 contains minor improvements over Rev4 but was never released.

## Rev6 (2026-05-10)

Rev6 is the first major integration release.

It adds:

- An integrated **STM32F103** for FlashFloppy OSD and keyboard control.
- An integrated **HD Audio** circuit for capturing Amiga audio.
- An integrated **HDMI switch** for selecting between the Raspberry Pi HDMI output and a second internal HDMI source.

This revision changes the board from primarily a video capture adapter into an integrated Amiga Audio/Video/Gotek OSD platform.

## Rev7 (2026-06-03)

Rev7 fixes bugs in Rev6 and adds a **CH340 USB-to-serial interface** for the STM32F103.

The USB-to-serial interface is intended to simplify STM32 development, programming, and diagnostics.

## Rev7.1 (2026-06-26)

Rev7.1 contains minor errata fixes intended to improve captured audio.

## Rev7.2 (2026-06-28)

Rev7.2 changes the secondary HDMI input connector from a male connector to a female connector.

This is the recommended connector arrangement for installations where a standard HDMI cable is to be connected to the secondary source.

## Revision selection

When building or installing a board, identify the PCB revision before beginning assembly.

The following documents should all correspond to the same revision:

- Schematic.
- PCB layout.
- Bill of materials.
- Assembly drawing.
- Programmable-device files.
- Raspberry Pi mounting instructions.
- STM32 firmware.

Do not use a connector, jumper, component value, or mounting position from a different revision without checking the corresponding schematic.

## Raspberry Pi orientation

The Raspberry Pi mounting arrangement changed during the project:

| Revision | General arrangement |
| --- | --- |
| Rev1 | Pi mounted face-up. |
| Rev2 | Face-up preferred; face-down option added. |
| Rev3 | Default face-down; middle position allows face-up. |
| Rev4 | Same general Rev3 arrangement. |
| Rev6+ | Pi mounting options reduced to a single face-down requirement. |

## Software common to all revisions

All board versions use the [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI) software on the Raspberry Pi.

The FlashFloppy OSD integration in the later hardware uses the board-specific [flashfloppy-osd-avhdmi](https://github.com/cdhooper/flashfloppy-osd-avhdmi) firmware.
