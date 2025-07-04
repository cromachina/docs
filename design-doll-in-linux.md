Running Design Doll in Linux

## Version
- Design Doll 6.0.0.0

## Other software to install in Linux
- Bottles (to configure Wine)
- 32-bit GPU support packages (including 32-bit Vulkan)
  - Installing these can vary per distribution and GPU, for example, on NixOS with an AMD GPU:
```nix
  hardware.graphics = {
    enable = true;
    enable32Bit = true;
  };
  hardware.amdgpu = {
    initrd.enable = true;
    opencl.enable = true;
    amdvlk.enable = true;
    amdvlk.support32Bit.enable = true;
  };
  services.xserver = {
    enable = true;
    videoDrivers = [ "amdgpu" ];
  };
```

## Bottle configuration
- Create a new Bottle with the `Custom` Environment
  - Set the architecture to `win32` (otherwise dotnet stuff may not run correctly)
- After finishing the Create step, under Options -> Settings:
  - Components (you may have to install some of these in Bottles main preferences)
    - Runner: `caffe-9.7`
    - DXVK: `dxvk-2.6.2`
- Under Options -> Dependencies, install:
  - `dotnet40`
  - `dotnet45`
  - `dotnet452`
  - `dotnet48`
  - `d3dx9`
  - `d3dcompiler_47`

## Install Design Doll
- Run the Design Doll Launcher from the Bottle
- If after running the actual Design Doll application, you get an error like a registry key needs to be set, then one of the dotnet installer manifests did not set it correctly:
  - Run the registry editor for the Bottle: Under Tools -> Registry Editor
  - Under `HKEY_LOCAL_MACHINE/Software/Microsoft/.NETFramework` add a new `String Value` called `InstallRoot` and set its data to the dotnet install location `C:\windows\Microsoft.NET\Framework`
  - This probably happened to me because I was messing around with Wine Mono (don't install this in the Bottle, it doesn't work with Design Doll).
 
## Other issues
- Some popup windows may render incorrectly, or just black (usually the exit-save confirmation popup)
  - Running in a Virtual Desktop seems to alleviate these kinds of issues.

- Sometimes there is a crash on exit with a COM exception.
  - This error seems to be harmless.

- Switching to another TTY will cause Design Doll to crash.
  - My guess is that it can't handle the display mode changing. Save your work and close Design Doll before you switch TTYs.
 
- Cannot import a model with an Import Tag because the file dialog doesn't work correctly. You click on the file to import and nothing happens.
  - While running in a Virtual Desktop, click on the start menu and select `Run...`, then enter `explorer` to open a file browser. Navigate to your model file and then drag and drop it into Design Doll's window. This will add an Import Tag to the currenctly selected object with the model loaded.
