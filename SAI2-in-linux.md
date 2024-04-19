Some issues with Paint Tool SAI2 in Linux and how to get around them:

## My system configuration:
- NixOS 23.11
- SAIv2 Preview.2024.04.10
- Wine 9.6 (nixos-unstable)
- OpenTabletDriver 0.6.4 (Built from source to add a new config)
- Wacom Cintiq 22HD (very old tablet from 2024)

## Issues:

### Simultaneously opening a bunch of files sometimes gives lots of errors:
This also causes SAI to become unusable and you likely have to restart the program.
- Solution: Opening each file one at a time seems to mitigate this issue.
- Technical: Something very bad happens in Wine's implementation of VirtualAlloc. Race condition?

---
### Drawing suddenly shows a "Discharge failed, Fatal" error or something like that:
It's not actually fatal, but it's still annoying.
- Solution: In SAI, go to "Other > Options > History and Recovery" and turn off "To suppress memory usage...".
  Note that this means file recovery doesn't work!
- Technical: SAI seems to have an issue with Wine's file system, in this particular case.

---
### The pen cursor seems to move away from the pen as you move it around:
This is most likely caused by the linux-wacom drivers that ship with your distro.
- Solution: Try using OpenTabletDriver instead.
- Technical: Linux-wacom drivers clash with SAI in some odd way, but they work fine in other apps like Krita.

---
### You open SAI while using OpenTabletDriver and it immediately has an error about WinTab API:
Pressure and other pen functions also seem to not work.
- Solution: Tap your pen on the tablet and reopen SAI.
- Technical: OpenTabletDriver seems to lazy-load tablets and they do not activate until a tablet event happens.

---
### The pen cursor seems to be offset a fixed distance from the pen:
This is caused by your window system and desktop environment. SAI wants to draw its own titlebar and frame,
and this clashes with your desktop environment's window frames.
- Solution: See if you can disable the desktop environment's titlebar and frame for SAI. For example,
  in KDE Plasma, you can right click on the titlebar and select "More Actions > No Titlebar and Frame".

---
### The pen cursor is STILL offset by a fixed distance, but only when SAI is fullscreen:
Again, it's caused by your window system. SAI's window geometry gets messed up when you hit the fullscreen button.
- Solution: Make SAI windowed and stretch its frame to the extents of your screen.

---
### Restarting your desktop environment makes SAI crash:
Like if you run `plasmashell --replace`, for example.
- Solution: Save your work and close SAI before restarting your desktop environment.

---
## Trapdoor
If you still have strange, unresolvable issues, you might have better luck running SAI inside of a Windows VM.
You will have to use USB redirection of the VM controller to give your tablet to the VM, otherwise pressure and tilt will not work.
You must also install either Wacom drivers or OpenTabletDriver inside of the VM.
