---
title: Frequently Asked Questions
layout: default
---
### Can Steamworks.NET be used within the Unity Editor?

Yes absolutely!

The only downside is the Steam Overlay does not function well or often even at all within the editor due to the number of unique windows, so keep that in mind when testing out aspects of Steamworks that require the Overlay to be available such as the Steam Controller or Microtransactions.

### Steam says I'm in game constantly after playing in the editor

Steam will only recognize a game as having shutdown when the last dependent process has quit. This is normal and does not affect the usage of Steamworks.NET.

### The Steam Overlay doesn't work or causes rendering errors!

There are many cases where the overlay collides with Unity, unfortunately it happens regardless of using Steamworks.NET and we are unable to resolve Steam Overlay issues as it is out of our control.

Some such causes that are known to cause the overlay to break are:

 * The Overlay is not supported in the editor.
 * The Overlay is not supported when running from outside of steam.¹
 * There are incompatabilities between Unity and the Steam Overlay when running under DirectX11

¹ For the Steam Overlay to work it must be injected before the renderer (DirectX/OpenGL) gets initialized. This is not feasible from Unity as Scripts only start running after the renderer is set up and injection is done in `SteamAPI.Init()`. It works when launched through Steam as Steam will always inject the overlay as the process starts.

### What languages does Steamworks.NET support?

In theory it should work with any .NET based language! Documentation is only provided for C#, but many people have used it from UnityScript as well. Let me know if you're using it from Boo!

### My Unity Standalone build doesn't launch!

You are likely missing the steam_appid.txt from your output folder. Steamworks.NET does not copy it over automatically because it is not something that you should be shipping to customers.

### Steam only shows me playing 'SpaceWar' how do I change it to my game?

When you launch your game from outside of Steam it relies on steam_appid.txt to be present to let steam know what is being launched. To change from SpaceWar to your own game you must edit the steam_appid.txt which is found in the root of your Unity project, or next to your built .exe/.app. Replace '480' with your Valve provided AppId.

### Does the Steam DRM wrapper work with Unity or XNA/Monogame?

No it doesn't. Unfortunately The DRM wrapper does not play well with .NET applications such as Unity and your application will likely fail to start if it's wrapped. Even if it does succeed and work for you it will often fail on other computers.

### Do I need the Pro version of Unity to use Steamworks.NET?

Officially Yes.

### The achievement popup only occurs after I quit my game.

This is most likely caused by not calling `SteamAPI.RunCallbacks()` in your game or not calling `SteamUserStats.StoreStats()` after you set an achievement.

### Does Steamworks.NET work with Unity 3.5 or earlier?

Not out of the box no. Unity 3.5 lacks some key features and supporting it was holding the rest of the project back.

If you wish to use Steamworks.NET with Unity 3.5 the following changes must be made:

* Remove BuildTarget.StandaloneOSXIntel64 and BuildTarget.StandaloneOSXUniversal from RedistCopy.cs
* Copy CSteamworks.dll from Assets/Plugins/x86 out to Assets/Plugins as Unity 3.5 did not support the x86 and x86_64 special folders and won't be able to find or copy the file over. (Feel free to delete the x86_64 folder, and the x86 folder after the fact. The .so files are for Linux only which Unity 3.5 did not support)
* Unity 3.5 did not support the UNITY_EDITOR_WIN platform define. This can cause a number of issues. The first is that steam_api.dll does not get copied over from Assets/Plugins/Steamworks.NET/redist to the Project root automatically, you must do this manually.
* The second issue that you must worry about is your build target while playing in the editor MUST be set to the editors platform. You will likely get a crash otherwise.