Running Paint Tool SAIv2 in Linux

## Version
- PaintTool SAI Ver.2 (64 bit) Preview.2024.11.23

## Other software to install in Linux
- Bottles (to configure a Wine prefix to run SAI)
- OpenTabletDriver (highly recommended because Linux-Wacom drivers do not play nice with SAI)

## Bottle configuration
- Create a new Bottle with the `Application` environment preset
- After finishing the Create step, under Options -> Settings:
  - Components (you may have to install some of these in Bottles main preferences)
    - Runner: `caffe-9.2` (latest version may have tablet issues, but this version seems to work fine)
    - DXVK: `Disabled` (SAIv2 does not use Direct3D, so this is unnecessary)
    - VKD3D: `Disabled`
- Under Options -> Dependencies, install:
  - `cjkfonts` (optional, might improve some UI font rendering, however this wont fix a lot of the missing fonts in the text tool)

## Installing
- Unzip SAI to a directory anywhere you want.
- Add sai2.exe to your Program shortcuts list in the Bottle.
- You install the license the same way as on Windows (see instructions on SAI's website.)

## Issues
- Do NOT save a file you are working on if you see lots of errors pop up:
  - There is a high chance that you will create a corrupted save file and lose your work. Try to save the work to a new file, and close and reopen SAI. If the new file is not corrupted, then that's great and you can continue work. If it is corrupted, well at least your old save should still be fine and hopefully you only lost minutes, instead of days.
  - This probably wont happen unless you hit some serious memory bugs in Wine (or run out of system memory).

- Bottles may sometimes freak out and kill SAI.
  - Not sure why this happens. Save your work before interacting with Bottles to mitigate the possibility of lost work.

- External clipboard paste into SAI sometimes doesn't work.
  - For whatever reason, it seems like the copy buffer for Wine has a small, limited size, and copying a large image will not work.
  - Solution: Screenshots do seem to be small enough to copy and paste. Alternatively, save the file you want to paste to disk and then open it with SAI directly.

- Copying from SAI to the external clipboard doesn't work.
  - Wine doesn't seem to interact with clipboard events from within SAI.
  - Alternative: Take a screenshot or export to a file.

- Drawing suddenly shows a "Discharge failed, Fatal" error or something like that. It's not actually fatal, but it's still annoying.
  - Solution: In SAI, go to "Other -> Options -> History and Recovery" and turn off "To suppress memory usage...". Note that this means file recovery doesn't work! Back up your files manually or with periodic copy job!!! I have a script that is meant to capture `sai2` files as they are saved and back up a few at a time to another location. You'll have to change the paths to be relevant for your own system: https://github.com/cromachina/art-scripts/blob/main/auto_backup.py
  - Reason: SAI seems to have an issue with Wine's file system, in this particular case. File recovery within SAI will not work either way.

- You open SAI while using OpenTabletDriver and it immediately has an error about WinTab API. Pressure and other pen functions also seem to not work. OpenTabletDriver lazy loads the tablet and it does not seem to start until the first tablet packet is received.
  - Solution: Tap your pen on the tablet to get OpenTabletDriver to start reading the tablet input and then reopen SAI.

- Desktop environment issues:
  - SAI randomly seems to freeze or stop accepting input, particularly after screen locking or sleep.
  - The SAI drawing cursor seems to be offset a fixed distance from the system mouse cursor.
  - Restarting your desktop environment makes SAI crash, for example, because your desktop environment crashed and restarted.
  - Solution: Running in a Virtual Desktop seems to alleviate these kinds of issues (Bottle -> Settings -> Display -> Advanced Display Settings -> Virtual Desktop). Close SAI and then set the virtual desktop size to the maximum geometry of all monitors of your monitors, like 3840x1080 if you have 2 1080p monitors side-by-side. Then put the virtual desktop window on your leftmost monitor and make it fullscreen. Your tablet mapping should also be on this leftmost screen. This is needed because SAI takes tablet coordinates relative to the corner of the virtual desktop, not your actual desktop, but OpenTabletDriver likely sends coordinates based on your system's total desktop geometry. If you don't do this, the coordinates in SAI when drawing will likely end up being squashed or offset. However, SAI seems to be more stable and resiliant to desktop environment issues when running inside of a virtual desktop.

- The mouse cursor (not the SAI drawing cursor position) has some constant offset from the pen's physical position. This is most noticable when using a screen tablet. Your tablet area needs to be calibrated.
  - Solution: In OpenTabletDriver, install the "Tablet Calibration" plugin and follow the instructions on the plugin's wiki.
