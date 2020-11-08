---
title: Installation
layout: default
---
The simple instructions to get you up and running with Steamworks.NET!

**To use _Steamworks.NET_ you must already be a Steamworks developer.**

## Unity Instructions

* Option A: Download the .unitypackage from the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases).
  * Import everything into your project's `Assets/` folder.
* Option B: Download the latest version from the [Github](https://github.com/rlabrecque/Steamworks.NET) master branch ([direct link](https://github.com/rlabrecque/Steamworks.NET/archive/master.zip)).
  * Copy everything into your project's `Assets/` folder. The `Standalone/` folder does not need to be imported.
  * You may wish to grab the [SteamManager](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) MonoBehaviour. It is included in the unitypackage.
* Launch your Unity project. The included editor scripts will copy steam_appid.txt into the root of your project.
* Open the `steam_appid.txt` which now resides in the root of your Unity project and replace `480` with your own AppId.
* Close Unity and relaunch the project so that it loads the freshly updated steam_appid.txt.
* Head over to [Getting Started](/gettingstarted/) to start using Steamworks.NET!

_Steamworks.NET_ is a free project but if it helps you make money please consider donating so that I can continue to support it in the years to come!

[![Support via Paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=YFZZER8VNXKRC)

Also never ever required but greatly appreciated is a mention in your credits! It's free, easy, and can help a lot! ([Examples](http://www.mobygames.com/developer/sheet/view/developerId,714285/))

---

## Non-Unity Instructions

If you are not using Unity then you have two available paths that you can take.

* Option A: The recommended route is to build the standalone assemblies with the project file located in `Standalone/` or download the prebuilt binaries which are available on the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases) page.
  * Open the Visual Studio solution (.sln) file, build both targets one for Windows and one for OSX & Linux. (Optional if you downloaded a prebuilt version)
  * Reference the built assembly (Steamworks.NET.dll) in your project.
  * Start coding! Call SteamAPI.Init() before initializing your renderer.
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

Upgrading Steamworks .NET is easy!

If you are using the unitypackage simply open the unitypackage within your Unity project and import everything, overwriting the existing files.

If you are upgrading to a source drop from github then clone or download the latest zip and copy the `Editor/` and `Plugins/` folders into your `Assets/` folder using Windows Explorer or Finder ensuring that you overwriting everything. If you try to drop these folders into the Unity Project view then the files are often duplicated. Then grab the latest [SteamManager](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) script and overwrite your old one, merging any changes you may have made to it.

---

## Uninstallation

_Steamworks.NET_ does not come with an easy one click uninstaller, but it stores no additional data and can be cleanly removed simply by removing the files that were initially imported. This can be quickly done simply by deleting the `Editor/Steamworks.NET`, `Plugins/Steamworks.NET`, `steam_api` binaries in `Plugins/x86[_64]`, the SteamManager.cs script, and then the `steam_appid.txt` in the root of your project.

---

## Using Steam Encrypted App Ticket?

The functions exposed via `sdkencryptedappticket.h` are supported in Steamworks.NET but you will need to manually place the `sdkencryptedappticket.dll/so/dylib` binaries in one of the following locations depending on the platform:

* Windows: Next to steam_api.dll
* OSX: In `/Contents/Frameworks/MonoEmbedRuntime/osx/`
* Linux: Next to libsteam_api.so

`sdkencryptedappticket.dll` can be found in the Steamworks SDK.
