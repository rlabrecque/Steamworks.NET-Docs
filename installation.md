---
title: Installation
layout: default
---
The Simple instructions to get you up and running with Steamworks.NET!

**To use _Steamworks.NET_ you must already be a Steamworks developer.**

## Unity Instructions
Steamworks.NET requires Unity Pro for the plugin functionality.

* Download the .unitypackage [Stable (6.0.0)](https://github.com/rlabrecque/Steamworks.NET/releases/download/6.0.0/Steamworks.NET_6.0.0.unitypackage) or Clone from [Github](https://github.com/rlabrecque/Steamworks.NET)
* Import everything into your `Assets/` folder.
* Launch your Unity project. The included editor scripts will copy steam_appid.txt (and steam_api.dll if your on windows) into the root of your project.
* Open `steam_appid.txt` which now resides in the root of your Unity project and replace `480` with your own AppId.
* Close Unity and relaunch the project so that it loads the freshly copied steam_appid.txt & steam_api.dll.
* You are ready to start coding with Steamworks.NET!

Head over to [Getting Started](/gettingstarted/) to start using Steamworks.NET

---

## Non-Unity Instructions

If you are not using Unity then you have two available routes that you could take.

* A: The peferable route is to build the standalone assemblies with the project file located in `Standalone/`. Alternatively you can download the prebuilt binaries which are available on the [Releases](https://github.com/rlabrecque/Steamworks.NET/releases) page.
	* Open the Visual Studio solution (.sln) file, build both targets one for Windows and one for OSX & Linux. (Optional if you downloaded a prebuilt version)
	* Reference the built assembly (Steamworks.NET.dll) in your project.
	* Start coding! Call SteamAPI.Init() before starting up your renderer.
* B: Copy `Plugins/Steamworks.NET` into your C# project. In Visual Studio open your project properties, change to the Build tab and define `STEAMWORKS_WIN` or `STEAMWORKS_LIN_OSX` globally via `Conditional compilation symbols`.
	* This is only recommended if your binary is not portable across platforms already. If you ship on multiple platforms you must have multiple build targets for each platforms. Please prefer the second route.
* When you build your application the following files must be copied into the output dir:
	* **steam_appid.txt** - Required for testing your application outside of steam. *Do not ship this to customers!*
	* **Steamworks.NET.dll** - Make sure it's the correct version for the platform that you plan on shipping for! - When building for OSX or Linux the wrong Steamworks.NET.dll will be copied over by default, it is recommended that you create a post build script to copy the correct version.
	* Windows:
		* **CSteamworks.dll**
		* **steam_api.dll**
	* OSX:
		* **CSteamworks.bundle**
		* **Steamworks.NET.dll.config** - Lets Mono know where to find CSteamworks
	* Linux:
		* **libCSteamworks.so**
		* **libsteam_api.so**

Head over to [Getting Started](/gettingstarted/) to start using Steamworks.NET

---

## Using Steam Encrypted App Ticket?

The functions exposed via `sdkencryptedappticket.h` are supported in Steamworks.NET but you will need to manually place the `sdkencryptedappticket.dll/so/dylib` binaries in the following locations depending on the platform:

* Windows: Next to steam_api.dll
* OSX: In `/Contents/Frameworks/MonoEmbedRuntime/osx/`
* Linux: Next to CSteamworks.so

`sdkencryptedappticket.dll` can be found in the Steamworks SDK.
