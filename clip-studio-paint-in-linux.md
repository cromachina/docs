Running Clip Studio Paint in Linux

## Version
- Clip Studio Paint Ex (64 bit) Version 2.0.6

## Other software to install in Linux
- Bottles (to configure Wine)
- OpenTabletDriver (recommended for pen pressure support)

## Bottle configuration
- Create a new Bottle with the `Application` environment preset
- After finishing the Create step, under Options -> Settings:
  - Components (you may have to install some of these in Bottles main preferences)
    - Runner: `caffe-9.2` (latest version may have tablet issues, but this version seems to work fine)
    - DXVK: `dxvk-2.5.2` (might make 3D materials have better performance)
    - VKD3D: `vkd3d-proton-2.14.1`
- Under Options -> Dependencies, install:
  - `cjkfonts` (lets UI font display correctly for things like Japanese assets/materials/brushes)
- Under Options -> Settings -> Compatibility:
  - Windows Version: `Windows 8.1` (The Windows 10 setting may cause CSP to crash on start)

## Installing
- Run the Clip Studio installer from the Bottle
- You can add Clip Studio Paint to the list of program shortcuts instead of opening the Clip Studio launcher every time. Assuming the default install location, you'll find at a path like this:
  - `/home/<USER>/.local/share/bottles/bottles/<NAME OF BOTTLE>/drive_c/Program Files/CELSYS/CLIP STUDIO 1.5/CLIP STUDIO PAINT/CLIPStudioPaoint.exe`
- You can activate your license by logging into your CELSYS account through Clip Studio (or in Clip Studio Paint -> Help -> Review/Change License).

## Other issues
- A popup telling you that your operating system is possibly incompatible with Clip Studio Paint (this is caused by setting the compatibility of the Bottle to Windows 8.1)
  - You can simply ignore it: Select "don't show this again" and close the message.

- Popup menus for brush pressure settings quickly disappear; Window geometry is sort of messed up in your desktop; Other weird window issues, etc.:
  - Running in a Virtual Desktop seems to alleviate these kinds of issues (Bottle -> Settings -> Display -> Advanced Display Settings -> Virtual Desktop). Close CSP and then set the virtual desktop size to the maximum geometry of all monitors of your monitors, like 3840x1080 if you have 2 1080p monitors side-by-side. Then put the virtual desktop window on your leftmost monitor and make it fullscreen. Your tablet mapping should also be on this leftmost screen. You need the virtual desktop to be the same geometry as your actual desktop or tablet coordinates may not work correctly in this mode. Popups for pressure may fall partly onto the other screen, as well other popups. You can use a single screen resolution for the virtual desktop, but then you need to use `mouse mode in tablet driver settings` in CSP's tablet preferences (but this will make you lose a ton of tablet resolution). However, eince these issues are so minimal and using a virtualdesktop brings about other issues, it's probably better to not use a virtual desktop, but the option is there if you really need it.
  - Alternatively, if you hold your mouse button down while hovering over popup menus, the menus stay open.

- Pen has no pressure, despite using and configuring OpenTabletDriver; Or the pen drawing position is offset or seems like it's not even showing up on the canvas:
  - In Clip Studio Paint: File -> Preferences -> Tablet:
    - Enable `Set tablet operation area with application`
    - Run on all monitors
    - Operate with whole tablet
  - This only works well either without a virtual desktop, or with a virtual desktop with the size of your total desktop geometry.
   
- You suddenly cannot click on anything and it seems like Clip Studio Paint is frozen:
  - Most likely you have a modal popup open (like Manage Fonts) and it's behind the main window (because you clicked on the main window after opening it). You should still be able to move the main window out of the way to close the popup.

- The mouse cursor has some constant offset from the pen's physical position. This is most noticable when using a screen tablet.
  - Your tablet area needs to be calibrated. In OpenTabletDriver, install the "Tablet Calibration" plugin and follow the instructions on the plugin's wiki.

- Resizing UI panels is a bit laggy.
  - I'm not sure what the issue is (probably Wine overhead), but CSP does have performance issues in general (CSP version 2 is a single threaded program without any async UI). Maybe it works better in CSP 3, but I haven't tested that version myself.

- Switching brushes with hotkeys is laggy.
  - Same problem as above, but you can mitigate it by hiding the brush and tool panels while you work (click the chevron icons `>` or `>>`). Hiding the panels prevents a UI refresh, which is the main source of the lag. It wont completely eliminate the lag.
