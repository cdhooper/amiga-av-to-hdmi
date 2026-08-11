# FlashFloppy OSD

The Amiga Pi to HDMI board integrates an STM32F103 running a board-specific version of FlashFloppy OSD.

The firmware repository is:

[cdhooper/flashfloppy-osd-avhdmi](https://github.com/cdhooper/flashfloppy-osd-avhdmi)

[Pre-compiled release binaries are available](https://github.com/cdhooper/flashfloppy-osd-avhdmi/releases)

The project is a fork of:

[keirf/flashfloppy-osd](https://github.com/keirf/flashfloppy-osd)

## What FF OSD provides

- FlashFloppy display output on the Amiga's HDMI display.
- Optional control of FlashFloppy using the Amiga keyboard.
- An emulated LCD I2C interface, overlayed on the Pi's HDMI display output.

## FlashFloppy setup

FlashFloppy must be installed separately on the Gotek.

For general setup, see the [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup) documentation.

The upstream FlashFloppy documentation describes the display configuration and the supported image-navigation modes. Native mode is generally the simplest mode when an LCD/OLED-style display is available.

## Hardware connection

The Amiga AV to HDMI board integrates the FF OSD controller, so an external FF OSD board is not required.

The STM32 interface to the Gotek captures the FlashFloppy OSD display interface.

Connections relative to the upstream FF OSD hardware documentation:

| Signal | Original BluePill FF OSD connection | Amiga AV to HDMI connection |
| --- | --- | --- |
| SCL | B6 | Gotek OLED I2C **SCL** |
| SDA | B7 | Gotek OLED I2C **SDA** |
| VCC | 3.3 V | DO NOT CONNECT |
| GND | G | Gotek OLED I2C **GND** |

It is not necessary to add pull-up resistors on the I2C signals, as the
Amiga Pi to HDMI board integrates these.

For the complete upstream connection description, see [Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections).

## Building the firmware

The board-specific firmware repository contains the source, Makefile, and build instructions.

Clone the repository from a Linux host with the gcc ARM compiler installed:

```sh
git clone https://github.com/cdhooper/flashfloppy-osd-avhdmi.git
cd flashfloppy-osd-avhdmi
make
```

Once the firmware is successfully built, the firmware will be in the
`src/FF_OSD.bin` file.

Firmware built directly from _keirf/flashfloppy-osd_
will not work on the Amiga AV to HDMI board because that firmware assumes
a fixed 8 MHz input clock to the STM32.

## Programming the STM32

Rev7 and later boards include a CH340 USB-to-serial interface connected to the STM32. If your board is not a Rev 7 or higher, you will need to procure a USB to TTL serial adapter, such as the FT232RL, and connect that to the Console port of the Amiga AV to HDMI board.

The exact programming procedure depends on the bootloader and programming tools used. There are several tool choices available.

1. Connect computer to USB-C port on video board.
2. Locate your FF_OSD.bin -- you may build your own from the
   [Flashfloppy OSD repository](https://github.com/cdhooper/flashfloppy-osd-avhdmi)
   or you may grab a copy of the latest release here:
   [Flashfloppy OSD releases](https://github.com/cdhooper/flashfloppy-osd-avhdmi/releases).
3. Program the firmware. From Linux:
        Determine which tty was connected. Example:
             `ch341-uart converter now attached to ttyUSB0`
        Method 1:
            Install [STM32CubeProgrammer](https://www.st.com/content/st_com/en/stm32cubeprogrammer.html)
            Hold down the DFU button next to the STM32 and enter the
            following, making sure to change the /dev/ttyUSB0 below to
            match where the CH340 appeared:
```             cd flashfloppy-osd-avhdmi
                DEV=/dev/ttyUSB0 make dfu```
                [Example output](osd_prog_stm32cube.txt)
            or
```             DEV=/dev/ttyUSB0
                sudo STM32_Programmer_CLI -c port=$DEV br=115200 -v -w FF_OSD.bin 0x08000000
                sudo STM32_Programmer_CLI -c port=$DEV br=115200 -g 0x08000000```
        Method 2:
            Install [stm32loader](https://github.com/jsnyder/stm32loader)
```             pip install stm32loader```
            Now that the programmer is installed, hold down the DFU button
            next to the STM32 and use the following command to program.
            Be sure to change /dev/ttyUSB0 below to where the CH340 appeared:
```             cd flashfloppy-osd-avhdmi/src
                sudo stm32loader --port /dev/ttyUSB0 -b 115200 -a 0x08000000 -w -v FF_OSD.bin -g 0x08000000 -f F1```
        Method 3:
            Install the open source stm32flash
```             git clone https://github.com/FYSETC/stm32flash
                cd stm32flash
                make install```
            Now that the programmer is installed, hold down the DFU button
            next to the STM32 and use the following command to program.
            Be sure to change /dev/ttyUSB0 below to where the CH340 appeared:
```             cd flashfloppy-osd-avhdmi/src
                DEV=/dev/ttyUSB0
                sudo stm32flash -b 115200 -vw FF_OSD.hex $DEV
                sudo stm32flash -b 115200 -h 0 $DEV```
        Method 4:
            Use ST-Link hardware to program the STM32.
            Get, build, and install stutils:
```             git clone https://github.com/texane/stlink.git stutils
                make -C stutils CMAKEFLAGS="-DCMAKE_INSTALL_PREFIX=. -DCMAKE_INSTALL_FULL_DATADIR=." -j4
                sudo make -C stutils install```
            Connect the programmer to the appropriate STM32 SWD pins
            on the Amiga AV to HDMI board.
            Use the following command to program:
```             sudo st-flash --reset write FF_OSD.bin 0x08000000```
                [Example output](osd_prog_st-link_stutils.txt)

Confirm that the LED on the Amiga AV to HDMI board is now illuminated.
If not, try a different programming method.

See the section below for how to verify other FlashFloppy OSD features.


## Verifying OSD communication

A simple test is:

1. Power off the Amiga.
2. Connect the FlashFloppy Gotek and optionally the keyboard control signals.
3. Power on the Amiga and wait for the RGBtoHDMI display to start.
5. Verify that FlashFloppy display text appears in the RGBtoHDMI video output.
6. Verify keyboard control if connected (use Left-Ctrl Left-Amiga and Cursor keys).

If the OSD remains blank, troubleshoot in this order:

1. STM32 firmware.
2. STM32 power and reset.
3. Gotek power.
4. I2C wiring.
5. I2C pull-ups.
6. FlashFloppy firmware/configuration.
7. RGBtoHDMI OSD/display configuration.

## OSD Configuration by Keyboard

The integrated STM32 can optionally use the Amiga keyboard for
configuration or to control FlashFloppy.

Connection by keyboard requires that you attach the KBCLK and KBDAT
pins from the Amiga AV to HDMI board to suitable locations on your
Amiga motherboard or keyboard controller.

| Model   | KBCLK location       | KBDAT location |
| ---     | ---                  | --- |
| A2000   | FB302 or RP300 pin 9 | FB303 or RP300 pin 10 |
| A3000   | RP360 pin 9          | RP350 pin 10 |
| A3000T  | RP360 pin 9          | RP350 pin 10 |
| A4000   | D363                 | D362 |
| A4000   | D363 or U209 pin 5   | D362 or U141 pin 12 |
| A4000CR | D363 or U209 pin 5   | D362 or U141 pin 12 |
| A4000T  | U520 pin 9           | U525 pin 6 |

See [Amiga keyboard control of FlashFloppy OSD](#Amiga keyboard control of FlashFloppy OSD) for a detailed list of all Amiga keystrokes and the function they perform.
Press Left Control-Left Alt-Help. This will bring up the configuration menu of the On-Screen Display, similar to:```
    FF OSD v1.9.c1
    Flash Config```
Press Left Control-Left Alt-Cursor Up 8 times. You will see:```
    H.Sub (0-30):
    0```
Press Left Control-Left Alt-Cursor Right 6 times. You will see:```
    H.Sub (0-30):
    6```
The text on screen will probably be less jittery, but unfortunately will always have some jitter. Try other values by pressing either Ctrl-Alt-Cursor Left or Ctrl-Alt-Cursor Right to pick the best choice.
Press Ctrl-Alt-Cursor Up 2 times when done.
Your updated configuration is now saved.
You can adjust other display settings just as easily. Just start again by pressing Ctrl-Alt-Help.

## OSD Configuration by USB-C

The OSD may also be configured by USB-C from a host PC with
the following serial settings: 115200, 8, N, 1.
If this is the first time setting up the FlashFloppy OSD, you'll
see serial output [similar to this](osd_serial_connect_new.txt)

The three control keys that FlashFloppy OSD responds to are:
| Key   | Operation |
| ---   | --- |
| Space | Select / Save Configuration |
| O     | Down / Next choice |
| P     | Up / Previous choice |

Press Space on the serial port. The screen will dim and text similar to the following appears:```
    FF OSD v1.9.c1
    Flash Config```
The screen text is likely jittery. On the serial port, you'll see text similar to:```
    FF OSD v1.9.c1
    Flash Config```
Press Space 8 more times. On the screen, you'll see:```
    H.Sub (0-30):
    0```
and the serial port:```
    Sync Polarity: Low
    Pixel Timing: 15kHz
    Display Height: Normal
    Display Output: PB15/SPI2
    Display Enable: None
    H.Off (1-199): 42
    V.Off (2-299): 50
    H.Sub (0-30): 0```
Press P 6 times. Screen:```
    H.Sub (0-30):
    6```
Serial:```
    H.Sub (0-30): 6```
The text on screen will probably be less jittery, but unfortunately will always have some jitter. Try other values by pressing either O or P to pick the best choice.
Press Space 2 times when done. The screen will return to normal brightness, and the serial port will display:```
    Save New Config? Save

    Current config:
     Sync Polarity: Low
     Pixel Timing: 15kHz
     Display Height: Normal
     Display Output: PB15/SPI2
     Display Enable: None
     H.Off: 42, Sub: 6
     V.Off: 50
     Rows: 2
     Columns: 16-40```
Your updated configuration is now saved.
You can adjust other display settings just as easily. Just start again by pressing Space.

## Amiga keyboard control of FlashFloppy OSD

The FlashFloppy OSD, can snoop or steal the signals coming from the keyboard. The keyboard input is then translated into FlashFloppy controls.

The FlashFloppy OSD supports a number of keyboard commands
to change operation of both the display and also FlashFloppy.

| Key | Function |
| --- | --- |
| Ctrl-Alt-Del        | OSD Off/On toggle |
| Ctrl-Alt-W          | Move OSD up |
| Ctrl-Alt-A          | Move OSD left |
| Ctrl-Alt-S          | Move OSD down |
| Ctrl-Alt-D          | Move OSD right |
| Ctrl-Alt-1          | Switch to primary HDMI |
| Ctrl-Alt-2          | Switch to secondary HDMI |
| Ctrl-Alt-CRSR Left  | Change selection (-) |
| Ctrl-Alt-CRSR Right | Change selection (+) |
| Ctrl-Alt-CRSR Up    | Select or Eject |
| Ctrl-Alt-Help       | Setup |
| Ctrl-Alt-Return     | Toggle Hold Keyboard (all keys captured by OSD) |
| Ctrl-Alt-Del        | OSD on/off (see header with U0-U3) |
| Ctrl-Alt-F1-F10     | Option |
| Ctrl-Alt-KP+        | Video Polarity  + |
| Ctrl-Alt-KP-        | Video Polarity  - |
| Ctrl-Alt-KP(        | Video 15KHz |
| Ctrl-Alt-KP)        | Video VGA |
| Ctrl-Alt-KP/        | Video Auto |

## Upstream documentation

Useful upstream references:

- [FF OSD Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections)
- [FF OSD Building From Source](https://github.com/keirf/flashfloppy-osd/wiki/Building-From-Source)
- [FF OSD Firmware Programming](https://github.com/keirf/flashfloppy-osd/wiki/Firmware-Programming)
- [FF OSD Configuring](https://github.com/keirf/flashfloppy-osd/wiki/Configuring-FF-OSD)
- [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup)
