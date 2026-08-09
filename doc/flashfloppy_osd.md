# FlashFloppy OSD

The Amiga Pi to HDMI board integrates an STM32F103 running a board-specific version of FlashFloppy OSD.

The firmware repository is:

[cdhooper/flashfloppy-osd-avhdmi](https://github.com/cdhooper/flashfloppy-osd-avhdmi)
[Pre-compiled release binaries are available](https://github.com/cdhooper/flashfloppy-osd-avhdmi/releases)

The project is a fork of:

[keirf/flashfloppy-osd](https://github.com/keirf/flashfloppy-osd)

## What FF OSD provides

The firmware provides:

- FlashFloppy display output on the Amiga's HDMI display.
- Optional control of FlashFloppy using the Amiga keyboard.
- An emulated LCD I2C interface, overlayed on the Pi's HDMI display output.

## FlashFloppy setup

FlashFloppy must be installed separately on the Gotek.

For general setup, see the [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup) documentation.

The upstream FlashFloppy documentation describes the display configuration and the supported image-navigation modes. Native mode is generally the simplest mode when an LCD/OLED-style display is available.

## Hardware connection

The Amiga AV to HDMI board integrates the FF OSD controller, so an external FF OSD board is not required.

The STM32 interface to the Gotek follows the FlashFloppy OSD display interface.

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
Build the firmware using the procedure documented by that repository.

Do not assume that a binary built for a different FF OSD hardware target is compatible with the Amiga Pi to HDMI STM32.

## Programming the STM32

Rev7 and later boards include a CH340 USB-to-serial interface connected to the STM32. If your board is not a Rev 7 or higher, you will need to procure a USB to TTL serial adapter, such as the FT232RL, and connect that to the Console port of the Amiga AV to HDMI board.

The exact programming procedure depends on the bootloader and programming tools used. There are several tool choices you can use.

1. Connect computer to USB-C port on video board.
2. Locate your FF_OSD.bin -- depending on when the first release is done,
   you might find a compiled release of this firmware in the
   [Flashfloppy OSD repository](https://github.com/cdhooper/amiga-pi-to-hdmi)
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

## Amiga keyboard control

The integrated STM32 can optionally use the Amiga keyboard to control FlashFloppy.

This is an FF OSD feature, where it can snoop the signals coming from the keyboard. The keyboard interface signals are processed by the STM32 and translated into FlashFloppy controls.

## Upstream documentation

Useful upstream references:

- [FF OSD Hardware Connections](https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections)
- [FF OSD Building From Source](https://github.com/keirf/flashfloppy-osd/wiki/Building-From-Source)
- [FF OSD Firmware Programming](https://github.com/keirf/flashfloppy-osd/wiki/Firmware-Programming)
- [FF OSD Configuring](https://github.com/keirf/flashfloppy-osd/wiki/Configuring-FF-OSD)
- [FlashFloppy Initial Setup](https://github.com/keirf/flashfloppy/wiki/Initial-Setup)
