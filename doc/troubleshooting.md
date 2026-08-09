# Troubleshooting

Troubleshooting is easiest when the system is divided into functional blocks.

```text
Amiga video -> capture/CPLD -> Raspberry Pi/RGBtoHDMI -> HDMI switch -> display
Amiga audio -> HD Audio capture ----------------^        ^  ^
FlashFloppy -> STM32 FF OSD -----------------------------/  |
Secondary HDMI source -> HDMI switch -----------------------/
```

## No HDMI output

Check:

1. Amiga power.
2. Board seating in the Video Slot.
3. Raspberry Pi power.
4. Raspberry Pi orientation.
5. RGBtoHDMI SD card.
6. HDMI cable.
7. HDMI display input.
8. HDMI switch selection.

If the Pi boots but no Amiga video is displayed, continue with the video-capture checks below.

## Raspberry Pi orientation

The Pi should be face-down on the Amiga AV to HDMI board, with the
mini HDMI connector facing the rear HDMI connector.
An incorrectly oriented Pi can cause serious problems.

## Video sparkle or unstable capture

- CPLD programming.
- Video-slot connection.
- Pi GPIO connection.
- RGBtoHDMI software/configuration. The RGBtoHDMI Sampling menu does allow adjustment of settings which can reduce or eliminate sparkle.

## RGBtoHDMI starts but the image is wrong

Verify that the RGBtoHDMI software is configured for the Amiga hardware.

Do not use an arbitrary RGBtoHDMI profile intended for another computer or another capture board. I recommend you select Amiga 2000 for the main profile, and the sub-profile should be Amiga 2000 50 Hz or Amiga 2000 60 Hz, depending on your specific Amiga model.

See the [RGBtoHDMI project](https://github.com/hoglet67/RGBtoHDMI).

## No FlashFloppy OSD

Check:

1. FlashFloppy is running on the Gotek.
2. The STM32 status LED is on. This also indicates the correct `flashfloppy-osd-avhdmi` firmware is installed.
3. The Gotek display interface wiring is correct.
4. FlashFloppy software is new enough to support FF OSD.
5. RGBtoHDMI is configured to display the FF OSD data.
    `Settings / FFOSD Overlay = On`

## No keyboard control

Verify:

- The board revision supports the integrated STM32.
- The correct board-specific firmware is installed.
- The Amiga keyboard is operating normally.
- The STM32 keyboard interface is correctly connected.
- Keyboard control is enabled in the firmware/configuration.

## No audio, quiet audio, or mono audio

Check:

1. Audio circuit assembly.
2. Motherboard audio connections are not shorted by a bad cable.
3. Several Amiga models require audio cables to be inserted to eliminate mono Audio.
4. Your monitor supports HDMI audio, and specifically the version that the RGBtoHDMI provides. HDMI audio is a digital format and not all devices are compatible.
5. RGBtoHDMI Audio is configured as Enabled.

Rev7.1 contains fixes intended to improve captured audio. If you are running with an older board, ensure that R20 and R21 are 470 Ohm resistors.

## Secondary HDMI source does not appear

Check:

1. The secondary HDMI source is powered.
2. The source produces a valid HDMI signal.
3. The HDMI cable is correct.
4. The connector type matches the board revision.
5. The HDMI switch is selecting the secondary input.
6. Soldering of the HDMI switch. Since this is a device, if soldered manually, requires hot air soldering, it's possible that not all pins are making contact. It may be necessary to re-flow or re-solder this part.

Rev7.2 uses a female secondary HDMI connector; earlier revisions use a male connector.

## CH340 serial interface not detected

1. Connect the USB cable.
2. Verify that the host operating system has a CH340 driver installed (Linux natively supports this part, but Windows requires a driver).
3. Check that the host operating system detects a USB serial device.
4. Verify CH340 and USB-C connector soldering.
5. Verify USB data connections.

If the CH340 is detected but the STM32 is not responding, troubleshoot the serial path and STM32 firmware separately.

## Board causes Amiga instability

Immediately power down the Amiga and disconnect power.

Check:

- PCB orientation.
- Video Slot seating.
- Short circuits.
- Component placement.
- Raspberry Pi orientation.
- Mechanical interference.
- Damaged connectors.

Do not continue operating a board that causes abnormal Amiga power consumption, overheating, or instability. There is likely a defect on the board or Amiga which needs to be repaired.

## Revision-specific problems

Before diagnosing a hardware problem, record:

- PCB revision.
- Raspberry Pi model.
- RGBtoHDMI software revision.
- FlashFloppy version.
- FF OSD firmware revision.
- Amiga model.
- Symptoms and whether they occur before or after RGBtoHDMI starts.

This information is important because the board's video capture, Pi mounting, HDMI, audio, and FF OSD implementations are still evolving.

## Useful upstream documentation

- [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI)
- [RGBtoHDMI Hardware Guide](https://github.com/hoglet67/RGBtoHDMI/wiki/Hardware-Guide)
- [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup)
- [FF OSD Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections)
- [FF OSD Firmware Programming](https://github.com/keirf/flashfloppy-osd/wiki/Firmware-Programming)
