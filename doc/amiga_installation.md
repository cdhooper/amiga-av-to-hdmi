# Amiga Installation

The Amiga Pi to HDMI board is designed to install into the big-box Amiga Video Slot.

Supported systems include:

- Amiga 2000.
- Amiga 3000.
- Amiga 3000T.
- Amiga 4000, with the video limitations described below.
- Amiga 4000T, with the video limitations described below.
- Other reimplemented Amiga models which have a video slot such as A4000TX and AmigaPCI.

## Before installation

Complete the following before installing the board:

- Assemble and inspect the PCB.
- Set jumpers as required.
- Install the Raspberry Pi.
- Prepare the RGBtoHDMI SD card and install in the Raspberry Pi.

## After  installation

Complete the following after installing the board and powering on the Amiga.

- Using the RGBtoHDMI software, program the CPLD.
- Program and test the FlashFloppy OSD STM32F103.
- Verify the HDMI output.
- Verify FlashFloppy OSD if it will be used.
- Verify the secondary HDMI input if it will be used.
- Verify Keyboard input if it will be used.

## Safety

**Power off and disconnect the Amiga from the power outlet before opening the computer or installing the board.**

Do not insert or remove the board while the Amiga is powered.

The board connects directly to the Amiga Video Slot. Incorrect installation can damage both the Amiga and the adapter.

## Mechanical installation

1. Shut down the Amiga.
2. Disconnect power.
3. Remove the Amiga cover.
4. Locate the Video Slot.
5. Verify the orientation of the Amiga Pi to HDMI board.
6. Align the board with the Video Slot.
7. Insert the board fully and evenly.
8. Verify that the board does not contact nearby chassis metalwork or components.
9. Connect the HDMI cable.

## Raspberry Pi orientation

The correct Raspberry Pi orientation depends on the PCB revision.
Do not rely on the orientation of another revision when installing the board.

See [Hardware Revisions](hw_revisions.md).

## HDMI connection

The primary HDMI output is at the rear of the Amiga.

Connect a normal HDMI cable from the rear connector your display.

The board can also switch to the secondary internal HDMI source when that source is connected to this board. You can install J5 to select video from the secondary internal HDMI connector. If you have installed the FF_OSD software, you can also toggle between the two displays by a quick press of the "DFU" button. Further, if you have attached the Amiga keyboard inputs to the KBDAT and KBCLK header, you can also select between the displays using Ctrl-Amiga-1 and Ctrl-Amiga-2 on the keyboard.

## FlashFloppy connection

The integrated STM32F103 connects to the FlashFloppy Gotek through the FF OSD interface.

Follow [FlashFloppy OSD](flashfloppy_osd.md) for the required Gotek configuration and firmware.

Do not add an external FF OSD adapter when using the integrated STM32 interface unless the hardware configuration explicitly calls for it.

## Amiga keyboard

If keyboard control is enabled, the STM32 interfaces with the Amiga keyboard and provides the corresponding FlashFloppy control.

Verify keyboard operation after the board has been installed and the Amiga has been powered on.

## Audio

The integrated HD Audio circuit captures Amiga audio.

Connect or configure the downstream audio capture equipment according to the intended use of the board. Rev7.1 and later should be preferred when audio performance is important because Rev7.1 includes audio-related fixes.

## First power-up in the Amiga

After installation:

1. Confirm that no tools, screws, cables, or loose hardware remain inside the Amiga.
2. Confirm that the Amiga AV to HDMI is fully seated.
3. Confirm that the Raspberry Pi is correctly oriented.
4. Confirm that the HDMI cable is connected.
5. Confirm that the FlashFloppy wiring is correct.
5. Confirm that the Amiga KBCLK and KBDAT are connected, if desired.
6. Apply power.
7. Verify Raspberry Pi/RGBtoHDMI startup.
8. Verify Amiga video.
9. Verify HDMI output.
10. Verify FlashFloppy OSD.
11. Verify keyboard control.
12. Verify audio capture.
13. Verify secondary HDMI switching, if desired.

## A2000/A3000

The A2000 and A3000 are the primary target systems.

The board captures the native digital Amiga video through the Video Slot and sends it to the Raspberry Pi for RGBtoHDMI processing.

## A4000 limitations

The board also works with the A4000, but it is limited to:

- 12-bit video. The A4000 chipset supports 24-bit video, which will have shading clipped slightly, but will still work fine with capture at 12-bit.
- OCS resolution screen modes.

Do not expect AGA display modes to work, as they are typically at a faster
bit rate than what the RGBtoHDMI can capture.
