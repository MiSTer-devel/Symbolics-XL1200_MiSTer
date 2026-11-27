# Symbolics XL1200 for MiSTer

**This core was created with AI**

This core is a Symbolics XL1200 Lisp machine. It boots from a SCSI disk
image and runs Genera 8.3.

## Features

- Runs Genera 8.3 and its applications (Zmacs, Document Examiner, CLIM,
  Joshua, Statice, TeX, CL-HTTP and more) and the Genera 8.1.1 animation
  world (S-Paint, S-Geometry, S-Dynamics)
- The Ivory processor with its floating-point accelerator and 16 megawords
  (80 MB) of memory, the most an XL1200 took
- The 1152 × 880 black-and-white console, over HDMI or direct video
- A color screen: the Univision UDC-3400 board's 8-bit color, shown in
  place of the console with Pause/Break
- The Symbolics keyboard's keys on a PC keyboard, and the three-button
  mouse
- SCSI disks and a CD-ROM drive from image files; Genera writes to its disk
  and saves worlds on it
- Ethernet through the MiSTer, by cable or WiFi: ping, FTP, telnet and the
  CL-HTTP web server
- The calendar clock, set from the MiSTer's

## What you need

- The core and `MiSTer_xl1200` from [releases](releases/)
- A disk image with Genera on it. The core can run a prebuilt one,
  `boot0.vhd.7z` (Genera 8.3 with its applications): search
  [archive.org](https://archive.org) for its SHA-1,
  `53d11962da7163762fda8f6f0d9cd43a23c05a4c`. Or
  [build your own](docs/disk.md)
- A keyboard and mouse
- For direct video, a display that takes 1152 × 880 at 60 Hz (54 kHz)

## Setup

1. Copy the newest `XL1200_*.rbf` from [releases](releases/) to `_Computer`
   on the SD card.
2. Copy [MiSTer_xl1200](releases/MiSTer_xl1200) to the root of the SD card,
   next to `MiSTer`.
3. Add to `MiSTer.ini`:

   ```
   [XL1200]
   main=MiSTer_xl1200
   ```

4. Put the disk image in `games/XL1200/` as `boot0.vhd`; the core mounts it
   by itself.
5. Start XL1200 from the Computer menu.
6. At the `FEP Command:` prompt type `Hello`, then `Boot`.
7. When Genera is up, type `Login Lisp-Machine`.

`MiSTer_xl1200` is MiSTer's main program with this core's network and clock
added. Without it the core still runs, but Genera has no network and its
clock is off by your time zone.

The Symbolics keys (Select, Function, Abort, Help and the rest) are on the
function keys: [docs/keyboard.md](docs/keyboard.md).

Before you switch cores or copy the image off the card, type `Halt Machine`
in Genera. That leaves you at the FEP prompt: `Continue` goes back to
Genera, `Hello` and `Boot` start it again.

## With the prebuilt disk

On that disk, `Boot` starts Genera 8.3 with its applications:
[what's there and how to start each one](docs/applications.md).

Two more worlds load at the FEP prompt after `Hello`, in place of `Boot`:

- The Genera 8.1.1 animation world, with S-Paint, S-Geometry, S-Dynamics
  and the other S-Graphics programs:
  `Load World FEP7:>animation-mac-6-2-color.ilod`, then `Start`. It makes
  the color screen at boot; Pause/Break switches the picture to it. For
  Craig Reynolds' Boids, press Select G (S-Geometry) and type
  `(boids:demo)`; any key stops the flock
- Flavors Statice: `Load World FEP7:>Genera-8-3-flavors-statice.ilod`, then
  `Start`

## CD-ROM

Mount an `.iso` on the OSD's `SCSI CD-ROM 2` and Genera reads the disc as
`CDROM2:`. With the Genera 8.3 CD:

- `Show Directory CDROM2:>*.*`
- `Copy File CDROM2:>WORLDS>GENERA_8_3.ILOD FEP7:>Genera-8-3.ilod`
- in the FEP-Tape activity, `Read Image File CDROM2:>DISTRIBUTION>BASIC_WORLD.TAPE`

## Network

Genera joins your LAN through the MiSTer, at an address you give it. Other
machines can ping it and move files with FTP, and Genera's Terminal program
opens telnet sessions on your LAN or beyond it.

You need `MiSTer_xl1200` from [Setup](#setup), a free address on your LAN
(Genera 8.3 has no DHCP) and your router's address. The prebuilt disk
comes set to 192.168.50.92.

1. With a cable in the MiSTer, leave the OSD's Network option at `eth0`. Over
   WiFi, set it to `tap0` and add the [tap setup](docs/NOTES.md#wifi), with
   Genera's address in it, to the MiSTer's `user-startup.sh`.
2. In Genera, after `Login Lisp-Machine`, type `Edit File FEP7:>Hello.boot`.
3. Change the `Set Network Address` line to
   `Set Network Address CHAOS|1200,INTERNET|A.B.C.D;gateway=G.G.G.G`, with
   Genera's address and your router's (Insert types `|`).
4. Save with Control-X Control-S.
5. Press Select L and type `Halt Machine`, then `Yes`. At the FEP prompt
   type `Hello` and `Boot`.
6. From another machine, `ping A.B.C.D`.
7. Genera now notes "The namespace needs to be fixed" at boot. To stop
   that, once: `Edit Namespace Object Host XL1200`, right-click the old
   `INTERNET` address, `Remove this field`, then type `Save Object` and
   `Y`.

To try an address for one start only, type the line from step 3 at the FEP
after `Hello`.

Using it:

- FTP from another machine, in active mode, as `lisp-machine`. To fetch
  `>lispm>bank.lisp`:
  `curl -P - -u lisp-machine:x "ftp://A.B.C.D/%3Elispm%3Ebank.lisp" -o bank.lisp`.
  To send a file there:
  `curl -P - -u lisp-machine:x -T hello.text "ftp://A.B.C.D/%3Elispm%3Ehello.text"`
- Telnet from Genera: press Select T, type the host as `INTERNET|A.B.C.D`,
  and answer N until it offers TELNET, then Y.

How it works: [docs/NOTES.md](docs/NOTES.md#network).

## Gotchas

- **The core writes to the disk image, so keep a copy to start over from.**
- Disk images must be SCSI2SD dumps: each 1280-byte block in 1536 bytes.
- Disc images must be `.iso`; `.cue`/`.bin` and `.chd` aren't read.
- With no disk mounted the machine halts; mounting one starts it.
- Genera 8.3 doesn't accept dates after 1999: leave Clock at "28 years back" unless the world has the Y2K patch.
- Save World shows "System Shutdown ..." with the clock stopped for several minutes; wait for "Saved to".
- To compile files, log in first.
- Log in at Genera's console before using FTP from another machine. A connection made earlier hangs, and file saves with it, until you press Function 0 S at the console.
- Genera 8.3 has no telnet server; other machines reach it by FTP.
- Every XL1200 core has the same Ethernet address, so one per LAN.

## Troubleshooting

- "Unable to get time from the network or the calendar clock": set Clock to "28 years back".
- Genera's time is off by your time zone: `MiSTer_xl1200` isn't running; check the `[XL1200]` lines in `MiSTer.ini`.
- Genera asks for the date right after the MiSTer starts: it booted before the MiSTer had the time from the network. Type the date, or start the core again.
- No picture on direct video: the display has to take an 80 MHz, 54 kHz signal.
- Genera doesn't answer ping: check `MiSTer_xl1200` and its `MiSTer.ini` lines, the Network option (`eth0` with a cable, `tap0` over WiFi) and that the address is on your LAN (the prebuilt disk's is 192.168.50.92).
- A connection beyond your LAN fails just after Genera starts: try again in a minute.
- FTP connects but a transfer is refused: the client is in passive mode; use active mode.
- No FEP prompt: a debug build's Debug line shows where it stopped, [docs/NOTES.md](docs/NOTES.md#debug-line).

## More

- [Building a disk](docs/disk.md) from your own Genera media
- [Building the core and running the tests](docs/building.md)
- [How the core works](docs/NOTES.md), and what was found out about the
  machine

## Credits

- [MiSTer-devel](https://github.com/MiSTer-devel): the MiSTer framework and
  main program, whose Ethernet bridges for the Sun and NeXT cores this one
  follows.
- The [SparcStation 20 core](https://github.com/MiSTer-devel/SunSparcStation20_MiSTer):
  the DDR arbiter and the Ethernet mailbox client, used under the GPL.
- Douglas Fields, [symbolics.lisp.engineer](https://symbolics.lisp.engineer)
  and [genera-patches](https://github.com/LispEngineer/genera-patches):
  the XL1201 patches with the Y2K fix, and the XL1201 timings and
  photographs the notes rely on.
- [Bitsavers](https://bitsavers.org): the Symbolics and chip documentation.
- Richard Gabriel's *Performance and Evaluation of Lisp Systems*: the
  benchmarks and the 3600 times.
- Symbolics: Genera and the Ivory, and the Genera 8.3 sources on the CD,
  the reference for how the boards behave.
