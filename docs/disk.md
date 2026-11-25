# Building a disk

The [prebuilt disk](../Readme.md#what-you-need) is the quick way to a
running Genera. This is how to make one from your own media instead.

## What you need

- A Genera 8.3 world: `GENERA_8_3.ILOD` from the Genera 8.3 CD
- An XL1200 FEP disk image: a raw SCSI2SD dump with 1280-byte blocks, each
  in 1536 bytes ([NOTES.md](NOTES.md#fep-file-system-layout))
- Python 3

## Steps

1. Make the disk:

   ```
   python3 tools/fepfs.py mkworld FEPDISK.dd GENERA_8_3.ILOD boot0.vhd --blocks 240000 --paging 120000 --standalone
   ```

   Add `--internet A.B.C.D` to give Genera its address on your
   [network](../Readme.md#network).
2. Copy `boot0.vhd` to `games/XL1200/` and start the core. At the FEP prompt
   type `Hello`, `Load World FEP7:>Genera-8-3.ilod`, `Start`.
3. Once, in Genera: `Login Lisp-Machine`, `Create Directory XL1200:>sys>`,
   `Create Directory XL1200:>sys>site>`,
   `Copy File FEP7:>sys-translations.text XL1200:>sys>site>sys.translations`.
4. Save it: `Save World FEP7:>Genera-8-3-site.ilod`. Answer Y to the herald
   and the label and Yes to the boot file, press Return for its name, then
   Super-C (Windows-C) to write it.
5. From then on type `Hello` and `Boot` at the FEP.

## The full disk

The MacIvory factory disk image that circulates holds a Genera file system
with Joshua, Concordia, Statice, CLIM, the documentation and fonts, the
animation world and the Y2K patches. The disk tool reads it and puts all of
it on an XL1200 disk, the Y2K patch loaded into both worlds, so the OSD's
Clock can be "Today":
[NOTES.md](NOTES.md#the-macivory-disk-and-the-full-image).
`Hello` and `Boot` start Genera 8.3; for the animation world type `Hello`,
`Load World FEP7:>animation-mac-6-2-y2k.ilod`, `Start`.

## Gotchas

- Genera 8.3 doesn't accept dates after 1999: leave the OSD's Clock at "28 years back" unless the world has the Y2K patch.
- Save World shows "System Shutdown ..." with the clock stopped for several minutes; wait for "Saved to".

## Troubleshooting

- "This Lisp world has not been configured for use at the local site": the disk was built without `--standalone`.
- "Unable to change site to Standalone. The directory >sys does not exist": step 3 hasn't been done on this disk.
- A full GC freezes Genera: the paging file is smaller than physical memory; build the disk with `--blocks 240000`.
