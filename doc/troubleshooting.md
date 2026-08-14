# Troubleshooting

Troubleshooting is easiest when the system is divided into functional blocks.

```text
Amiga video -> capture/CPLD -> Raspberry Pi/RGBtoHDMI -> HDMI switch -> display
Amiga audio -> HD Audio capture ----------------^        ^  ^
Secondary HDMI source -> HDMI switch --------------------/  |
FlashFloppy -> FlashFloppy OSD -----------------------------/
Amiga Keyboard-----^  ^
Gotek OLED output-----/
```

## No HDMI output

Check:

1. Amiga power.
2. Board seating in the Video Slot.
3. Raspberry Pi orientation and seating.
4. RGBtoHDMI SD card.
5. HDMI cable.
6. HDMI display input selection.
7. Amiga AV to HDMI switch setting.

When you power on the Amiga, you should see the led on the Raspberry Pi
blink as it loads the RGBtoHDMI software from the SD card. This operation
does not take very long (a few seconds), so you should observe the board
immediately after turning on power.

If the Pi boots (you can see an OSD Menu when the top button is pressed)
but no Amiga video is displayed, continue with the video-capture checks below.

## Raspberry Pi orientation

The Pi should be face-down on the Amiga AV to HDMI board, with the
mini HDMI connector facing the rear HDMI connector.
An incorrectly oriented Pi can cause serious problems. You should
have previously installed the mini HDMI to 2x8 header adapter in the Pi,
and both of these are mounted together on the Amiga AV to HDMI board.

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
3. The Gotek display interface wiring is correct. You should connect the I2C SCL, SDA, and GND lines to appropriate pins on the Gotek.
4. FlashFloppy software is new enough to support FlashFloppy OSD.
5. RGBtoHDMI is configured to display the FlashFloppy OSD data.
    `Settings / FFOSD Overlay = On`

## No keyboard control

Verify:

1. The Amiga keyboard is operating normally.
2. You have chosen the correct location on the Amiga motherboard to tap the KBCLK and KBDAT pins.
3. The connection to the Amiga AV to HDMI board has been made for KBCLK and KBDAT.
4. The correct board-specific FlashFloppy OSD firmware is installed.

## No audio, quiet audio, or mono audio

Check:

1. Audio circuit assembly.
2. Motherboard audio connections are not shorted by a bad cable.
3. Several Amiga models require the RCA audio cables to be inserted to eliminate mono Audio.
4. Your monitor supports HDMI audio, and specifically the version that the RGBtoHDMI provides. HDMI audio is a digital format and not all devices are compatible.
5. RGBtoHDMI Audio is configured as Enabled.

Rev7.1 contains fixes intended to improve captured audio. If you are running with an older board, ensure that R20 and R21 are 470 Ohm resistors.

## Secondary HDMI source does not appear

Check:

1. The secondary HDMI source is powered.
2. The source produces a valid HDMI signal.
3. The HDMI cable is correct.
4. The HDMI switch is selecting the secondary input.
5. Soldering of the HDMI switch. Since this is a device, if soldered manually, requires hot air soldering, it's possible that not all pins are making contact. It may be necessary to re-flow or re-solder this part. With adequate flux. re-flow may be done with a fine tip soldering iron around the edges of the chip.

## CH340 serial interface not detected

1. Check the USB cable. A freshly soldered board may leave non-conducting flux in the USB-C connector, and it may be necessary to clean the connector with isopropyl alcohol.
2. Verify that the host operating system has a CH340 driver installed (Linux natively supports this part, but Windows requires a driver).
3. Check that the host operating system detects a USB serial device.
4. Verify CH340 and USB-C connector soldering.
5. Verify USB data connections.

If the CH340 is detected but the STM32 is not responding, troubleshoot the serial path and STM32 firmware separately. It's not necessary for the Amiga to be powered on for the CH340 to be detected, but the Amiga must be powered on for the STM32 to respond.

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
- FlashFloppy OSD firmware revision.
- Amiga model.
- Symptoms and whether they occur before or after RGBtoHDMI starts.

This information is important because the board's video capture, Pi mounting, HDMI, audio, and FlashFloppy OSD implementations are still evolving.

## Useful upstream documentation

- [RGBtoHDMI](https://github.com/hoglet67/RGBtoHDMI)
- [RGBtoHDMI Hardware Guide](https://github.com/hoglet67/RGBtoHDMI/wiki/Hardware-Guide)
- [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup)
- [FlashFloppy OSD Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections)
- [FlashFloppy OSD Firmware Programming](https://github.com/keirf/flashfloppy-osd/wiki/Firmware-Programming)
