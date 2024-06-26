Some issues with Paint Tool SAI2 in Linux and how to get around them:

## My system configuration:
- NixOS Unstable (24.11)
- SAIv2 Preview.2024.04.10
- Wine 9.6 (nixos-unstable)
- OpenTabletDriver 0.6.4 (Built from source to add a new config)
- Wacom Cintiq 22HD (very old tablet from 2014)

## Issues:

### Updating Wine may cause your license to become invalid:
Note that there are a limited number of times you can acquire a license update for SAI in a month,
so backup your previous license file before you want to change wine versions. SAI wants to use your hardware
identity to determine your license, however Wine's internals changing can cause this to also change.

---
### Do NOT save a file you are working on if you see lots of errors pop up:
There is a high chance that you will create a corrupted file and lose your work.
Try to save the work to a new file, and close and reopen SAI. If the new file is not corrupted,
then that's great and you can continue work. If it is corrupted, well at least your old save should
still be fine and hopefully you only lost minutes, instead of a day.

---
### Simultaneously opening a bunch of files sometimes gives lots of errors:
This also causes SAI to become unusable and you likely have to restart the program.
- Solution: Opening each file one at a time seems to mitigate this issue, but sometimes it still happens.
- Technical: Something very bad happens in Wine's implementation of VirtualAlloc. Race condition?

---
### Making lots of custom scatter brush types may cause the same issue as above in a different way:
You may have to avoid using this feature if you see lots of errors when you have many scatter brushes saved.

---
### Drawing suddenly shows a "Discharge failed, Fatal" error or something like that:
It's not actually fatal, but it's still annoying.
- Solution: In SAI, go to "Other > Options > History and Recovery" and turn off "To suppress memory usage...".
  Note that this means file recovery doesn't work! Back up your files manually or with periodic copy job!!!
- Technical: SAI seems to have an issue with Wine's file system, in this particular case.

---
### The drawing cursor in SAI seems to move away from the system mouse cursor position as you move it around:
This is most likely caused by the linux-wacom drivers that ship with your distro.
- Solution: Try using OpenTabletDriver instead.
- Technical: Linux-wacom drivers clash with SAI in some odd way, but they work fine in other apps like Krita.

---
### You open SAI while using OpenTabletDriver and it immediately has an error about WinTab API:
Pressure and other pen functions also seem to not work.
- Solution: Tap your pen on the tablet and reopen SAI.
- Technical: OpenTabletDriver seems to lazy-load tablets and they do not activate until a tablet event happens.

---
### The drawing cursor seems to be offset a fixed distance from the system mouse cursor:
This is caused by your window system and desktop environment. SAI wants to draw its own titlebar and frame,
and this clashes with your desktop environment's window frames.
- Solution: See if you can disable the desktop environment's titlebar and frame for SAI. For example,
  in KDE Plasma, you can right click on the titlebar and select "More Actions > No Titlebar and Frame".

---
### The drawing cursor is STILL offset by a fixed distance from the system mouse cursor, but only when SAI is fullscreen:
Again, it's caused by your window system. SAI's window geometry gets messed up when you hit the fullscreen button.
- Solution: Make SAI windowed and stretch its frame to the extents of your screen.

---
### The mouse cursor (not the drawing cursor) has some offset from the pen's physical position:
This is most noticable when using a screen tablet. Now that all of the weird window geometry stuff is resolved, your tablet area needs to be calibrated.
- Solution: In OpenTabletDriver, install the "Tablet Calibration" plugin and follow the instructions on the plugin's wiki.

---
### Restarting your desktop environment makes SAI crash:
Like if you run `plasmashell --replace`, for example.
- Solution: Save your work and close SAI before restarting your desktop environment.

---
### Systemd shutdown or reboot is delayed with "A stop job is running for User Manager..."
Sometimes running programs like SAI with wine leaves lingering wine service processes.
These seem to delay shutdown with systemd because it asks them to SIGTERM and they hang waiting for something.
You can fix this with a systemd shutdown script that just kills the lingering processes, for example I add this to my NixOS config:
```nix
  systemd.services."wine-shutdown" = {
    enable = true;
    serviceConfig = {
      Type = "oneshot";
      RemainAfterExit = true;
      ExecStop = "${pkgs.procps}/bin/pkill -9 .exe";
    };
    before = [ "shutdown.target" "reboot.target" "halt.target" ];
    wantedBy = [ "multi-user.target" ];
  };
```

---
### Trapdoor
If you still have strange, unresolvable issues, you might have better luck running SAI inside of a Windows VM.
You will have to use USB redirection of the VM controller to give your tablet to the VM, otherwise pressure and tilt will not work.
You must also install either Wacom drivers or OpenTabletDriver inside of the VM.
