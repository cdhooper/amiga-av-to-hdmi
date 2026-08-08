# amiga-pi-to-hdmi

This project implements a board which plugs into a big-box Amiga Video Slot
(A2000 or A3000) and uses a Raspberry Pi Zero to process the Amiga video output
in realtime to provide HDMI video output for the computer. It also partially
works with the A4000, but is limited to 12-bit video (the A4000 chipset
supports 24-bit) and only OCS resolution screen modes.

The Rev1 and Rev2 board schematic designs are based on the Denise socket
Amiga-Digital-Video module for OCS and ECS Amigas with a 48-pin DIP Denise:
    https://github.com/c0pperdragon/Amiga-Digital-Video

The Rev3 and Rev4 board schematic designs are based loosely on the A2000 CPLD video board by
LinuxJedi:
    https://github.com/LinuxJedi/AmigaRGBtoHDMI

All board versions use software for the Raspberry Pi Zero that is developed
by hoglet67's multi-platform vintage hardware RGBtoHDMI project:
    https://github.com/hoglet67/RGBtoHDMI

How to set up FlashFloppy OSD support:
    https://github.com/keirf/flashfloppy-osd/wiki/Hardware-Connections
    https://github.com/keirf/flashfloppy/wiki/Hardware-Mods#oled-display

----------------------------------------------------------------------

The Rev1 board was designed to provide a few options (OCS Denise only):
<OL>
<LI>The mini HDMI port of the Raspberry Pi Zero may be directly exposed
       to the rear of the computer, allowing one to plug directly into the
       Pi's video port.
</LI>
<LI>
    The Raspberry Pi Zero may be mounted in a second position which
       allows for an adapter cable so that a full size HDMI port may be
       exposed out the rear of the Amiga.
</LI>
<LI>
    A full size Raspberry Pi (such as the Pi 2 B+) may be mounted on
       the board with its HDMI port exposed on the rear of the Amiga.
</LI>
<P>

    In all the scenarios above, the Raspberry Pi must be mounted to the
    board face-up. This likely means that you will need to de-solder the
    connector from the Pi 2 B+ and higher to solder it on the back side of
    the Pi. Another option, if you are using a Pi Zero with a top side GPIO
    connector already attached, is that you could mount it on the rear of the
    board, and run a cable to the rear slot.
</OL>
<P>
The Rev2 board adds support for ECS Denise. It also adds:
<UL>
<LI>
    Rear-facing board-mounted HDMI female connector so that HDMI cables
    may be solidly mounted to the board.
</LI>
<LI>
    FlashFloppy OSD display support header.
</LI>
<LI>
    A small amount of timing adjustment to compensate for timing differences
    in some Amiga motherboards. The differences might result in video
    sparkle effects.
variations.
</LI>
<LI>
    An option to mount the Raspberry Pi face-down rather than face-up
    as is required in the Rev1 board. The preferred mode is still face-up,
    however. This is the middle Pi connector option on the board.
</LI>
</UL>
<P>
The Rev3 board changes the design by replacing all the discrete logic with
a single CPLD. The CPLD eliminates the need for a Denise type jumper or
any timing adjustment as it samples CSYNC to capture video timing information
rather than attempting to use the CDAC clock or reconstruct the 7M clock.
This makes Amiga video capture much more reliable.
<P>
Additional changes in the Rev3 board:
<UL>
<LI>
    HDMI passthrough connectors are moved closer together and traces are now
    length-matched to improve HDMI signal quality.
</LI>
<LI>
    Three button mode is now an option (there are now three rear-facing buttons
    where the Rev2 had only a single rear-facing button).
</LI>
<LI>
    The three Pi connector options are now rotated 180 degrees and flipped.
    This means the default mode for Pi connection is now face-down. This
    is desirable because most Raspberry Pi boards have the headers soldered
    on the top side. The middle Raspberry Pi connector option now provides
    the opposite -- the Pi can still be mounted face-up, but only in this
    position.
</LI>
</UL>
