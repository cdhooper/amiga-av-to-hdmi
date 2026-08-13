# System architecture

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

