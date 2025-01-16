Here are some issues with Paint Tool SAIv2 in Linux and how to mitigate them.

## My system configuration:
- NixOS Unstable
- Bottles (to configure a Wine prefix to run SAI)
- OpenTabletDriver 0.6.4 (Built from source to add a new config)
- Wacom Cintiq 22HD (very old tablet from 2014)

# SAI issues
Some of the below stability issues can be mitigated by using Bottles configurations, see here:
https://github.com/TibixDev/sai2-guide

### Updating Wine may cause your license to become invalid:
Note that there are a limited number of times you can acquire a license update for SAI in a month,
so backup your previous license file before you want to change wine versions. SAI wants to use your hardware
identity to determine your license, however Wine's internals changing can cause this to also change.

### Do NOT save a file you are working on if you see lots of errors pop up:
There is a high chance that you will create a corrupted file and lose your work.
Try to save the work to a new file, and close and reopen SAI. If the new file is not corrupted,
then that's great and you can continue work. If it is corrupted, well at least your old save should
still be fine and hopefully you only lost minutes, instead of days.

### Simultaneously opening a bunch of files sometimes gives lots of errors:
This also causes SAI to become unusable and you likely have to restart the program.
- Solution: Opening each file one at a time seems to mitigate this issue, but sometimes it still happens.
  This may also be mitigated by using different versions of Wine (try the Bottles suggestion above).
- Technical: Something very bad happens in Wine's implementation of VirtualAlloc. Race condition?

### Making lots of custom scatter brush types may cause the same issue as above in a different way:
You may have to avoid using this feature if you see lots of errors when you have many scatter brushes saved.
- Newer versions of Wine might mitigate some of these issues related to SAI opening many files at once.

### Drawing suddenly shows a "Discharge failed, Fatal" error or something like that:
It's not actually fatal, but it's still annoying.
- Solution: In SAI, go to "Other > Options > History and Recovery" and turn off "To suppress memory usage...".
  Note that this means file recovery doesn't work! Back up your files manually or with periodic copy job!!!
- Technical: SAI seems to have an issue with Wine's file system, in this particular case. File recovery will
  not work either way.

### The drawing cursor in SAI seems to move away from the system mouse cursor position as you move it around:
This is most likely caused by the linux-wacom drivers that ship with your distro.
- Solution: Try using OpenTabletDriver instead.
- Technical: Linux-wacom drivers clash with SAI in some odd way, but they work fine in other apps like Krita.
  This may also be a consequence of desktop environment issues like below, or even multi-monitor setups.

### You open SAI while using OpenTabletDriver and it immediately has an error about WinTab API:
Pressure and other pen functions also seem to not work.
- Solution: Tap your pen on the tablet and reopen SAI.

# Desktop environment issues

### SAI randomly seems to freeze or stop accepting input, but not directly crash, particularly after screen locking or sleep.
This seems to be caused by the window system and desktop environment.
- Solution: Run wine config and enable virtual desktop. Set the screen size to the maximum geometry of all monitors (like as shown
  in your tablet area config). You can then put the virtual desktop window on your leftmost monitor and make it fullscreen. This is
  needed because SAI takes tablet coordinates relative to the corner of the virtual desktop, not your actual desktop, but your tablet
  software most likely sends coordinates based on your system's total desktop geometry. If you don't do this, the coordinates in SAI when
  drawing will likely end up being squashed or offset (just like the offset issues below). However, SAI seems to be more stable and
  resiliant to desktop environment issues when running inside of a virtual desktop.

### The drawing cursor seems to be offset a fixed distance from the system mouse cursor:
This is caused by your window system and desktop environment. SAI wants to draw its own titlebar and frame,
and this clashes with your desktop environment's window frames.
- Solution: See if you can disable the desktop environment's titlebar and frame for SAI. For example,
  in KDE Plasma, you can right click on the titlebar and select "More Actions > No Titlebar and Frame".
  You can also disable decorations in the wine config tool.

### The drawing cursor is STILL offset by a fixed distance from the system mouse cursor, but only when SAI is fullscreen:
Again, it's caused by your window system. SAI's window geometry gets messed up when you hit the fullscreen button.
- Solution: Make SAI windowed and stretch its frame to the extents of your screen.

### The mouse cursor (not the drawing cursor) has some offset from the pen's physical position:
This is most noticable when using a screen tablet. Now that all of the weird window geometry stuff is resolved, your tablet area needs to be calibrated.
- Solution: In OpenTabletDriver, install the "Tablet Calibration" plugin and follow the instructions on the plugin's wiki.

### Restarting your desktop environment makes SAI crash:
Like if you run `plasmashell --replace`, for example.
- Solution: Save your work and close SAI before restarting your desktop environment, or run SAI in a virtual desktop like above (it won't crash in this case).

# Wine systemd issues

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

# Virtual machine trapdoor
If you still have strange, unresolvable issues, you might have better luck running SAI inside of a Windows VM.
You will have to use USB redirection of the VM controller to give your tablet to the VM, otherwise pressure and tilt will not work.
This also means that tablet is not usable outside of the VM while it's being redirected.
You must also install either Wacom drivers or OpenTabletDriver inside of the VM.
The negatives of this are that you have to set aside memory for exclusive use by the VM (I usually give it 16 GB). The VM may also
feel laggy on a slower computer or depending on your VM host software. VirtualBox has given me the best results so far.
