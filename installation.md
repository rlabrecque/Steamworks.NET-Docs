---
title: Installation
layout: default
---
The simple instructions to get you up and running with Steamworks.NET!

**To use _Steamworks.NET_ you must already be a Steamworks developer.**

## Unity Instructions

* Option A: .unitypackage
  1. Download the .unitypackage from the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases) page on GitHub.
  2. Import everything into your project.
* Option B: Unity Package Manager
  1. Open the Package Manager
  2. Click the "+" (plus) button located in the upper left of the window
  3. Select the "Add package from git URL..." option
  4. Enter the following URL: <https://github.com/rlabrecque/Steamworks.NET.git?path=/com.rlabrecque.steamworks.net>
     Optionally specify a version number at the end like so: <https://github.com/rlabrecque/Steamworks.NET.git?path=/com.rlabrecque.steamworks.net#20.0.0>
     This version number can be found on the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases) page on GitHub.
  5. Click the "Add" button and wait several seconds for the system to download and install the Steamworks.NET package from GitHub.
  6. Optionally: Manually download the [SteamManager](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) MonoBehavior which contains a starting point for using the Steamworks API in your project.
* Option C: Manual installation
  1. Download the latest version from the [GitHub](https://github.com/rlabrecque/Steamworks.NET) master branch ([direct link](https://github.com/rlabrecque/Steamworks.NET/archive/master.zip)).
  2. Copy the `com.rlabrecque.steamworks.net/` folder into your project's `Assets/` folder via explorer or finder, do not try to drag and drop it into Unity's Project window.
  3. Optionally: Manually download the [SteamManager](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) MonoBehaviour which contains a starting point for using the Steamworks API in your project.
* Once imported the included editor scripts will copy steam_appid.txt into the root of your project.
* Open the `steam_appid.txt` which now resides in the root of your Unity project and replace `480` with your own AppId. Ensure you save this file as ASCII or UTF-8 without a BOM.
* Close Unity and relaunch the project so that it loads the freshly updated `steam_appid.txt`.
* Head over to [Getting Started](/gettingstarted/) to start using Steamworks.NET!

_Steamworks.NET_ is a free project but if it helps you make money please consider donating so that I can continue to support it in the years to come!

[![Support via Paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=YFZZER8VNXKRC)

<iframe src="https://github.com/sponsors/rlabrecque/button" title="Sponsor rlabrecque" height="35" width="116" style="border: 0;"></iframe>

Also never ever required but greatly appreciated is a mention in your credits! It's free, easy, and can help a lot! ([Examples](http://www.mobygames.com/developer/sheet/view/developerId,714285/))

---

## Non-Unity Instructions

If you are not using Unity then you have two available paths that you can take.

* Option A: The recommended route is to build the standalone assemblies with the project file located in `Standalone/` or download the prebuilt binaries which are available on the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases) page.
  1. Open the Visual Studio solution (.sln) file, build both targets one for Windows and one for OSX & Linux. (Optional if you downloaded a prebuilt version)
  2. Reference the built assembly (Steamworks.NET.dll) in your project.
  3. Start coding! Call SteamAPI.Init() before initializing your renderer.
* Option B: Otherwise, copy `Plugins/Steamworks.NET` directly into your C# project. In Visual Studio open your project properties, change to the Build tab and define `STEAMWORKS_WIN` or `STEAMWORKS_LIN_OSX` globally via `Conditional compilation symbols`.
  * This is only recommended if your binary is not portable across platforms already. If you ship on multiple platforms you must have multiple build targets for each platforms. Please prefer the first route.
* When you build your application the following files must be copied into the output dir:
  * All Platforms:
    * **steam_appid.txt** - Required for testing your application outside of steam. **Do not ship this to customers!**
    * **Steamworks.NET.dll** - Make sure it's the correct version for the platform that you plan on shipping for! - When building for OSX or Linux the wrong Steamworks.NET.dll will be copied over by default, it is recommended that you create a post build script to copy the correct version.
  * Windows:
    * **steam_api.dll**
  * Linux:
    * **libsteam_api.so**

Head over to [Getting Started](/gettingstarted/) to start using Steamworks.NET

_Steamworks.NET_ is a free project but if it helps you make money please consider donating so that I can continue to support it in the years to come!
Also never ever required but greatly appreciated is a mention in your credits. ([Examples](http://www.mobygames.com/developer/sheet/view/developerId,714285/))

[![Support via Paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=YFZZER8VNXKRC)

---

## Upgrade Instructions

Unity upgrade notice: If you're upgrading from Steamworks.NET 15.0.1 or earlier to 20.0.0 or later, or switching between one of the 3 different installation types you MUST remove the previous install of Steamworks.NET before continuing as the project structure has changed to support the Unity Package Manager.

If you are upgrading with the Unity Package Manager, just re-import the package, and optionally bring in the latest changes from SteamManager into your project. If you're using the #version tag, you may need to uninstall your current release before reimporting a newer version.

If you're not using the Unity Package Manager, follow the Uninstallation steps below, then re-follow the installation steps above.

---

## Uninstallation

_Steamworks.NET_ does not come with an easy one click uninstaller, but it stores no additional data and can be cleanly removed simply by removing the files that were initially imported, or removing Steamworks.NET from the Unity Package Manager. This can be quickly done simply by deleting the `com.rlabrecque.steamworks.net` directory, the SteamManager.cs script, and then the `steam_appid.txt` in the root of your project.

---

## Using Steam Encrypted App Ticket?

The functions exposed via `sdkencryptedappticket.h` are supported in Steamworks.NET but you will need to manually place the `sdkencryptedappticket.dll/so/dylib` binaries in one of the following locations depending on the platform:

* Windows: Next to steam_api.dll
* OSX: In `/Contents/Frameworks/MonoEmbedRuntime/osx/`
* Linux: Next to libsteam_api.so

`sdkencryptedappticket.dll` can be found in the Steamworks SDK.
