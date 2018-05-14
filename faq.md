---
title: Frequently Asked Questions
layout: default
---
### Can Steamworks.NET be used within the Unity Editor?

Yes absolutely!

The only downside is the Steam Overlay does not function well or even often at all within the editor due to the number of unique windows, so keep that in mind when testing out aspects of Steamworks that require the Overlay to be available such as the Steam Controller or Microtransactions.

### How do I upload my game to Steam?

You still need the official Steamworks SDK to get the ContentBuilder tool to upload your game.

### Steam says I'm in game constantly after playing in the editor

Steam will only recognize a game as having shutdown when the last dependent process has quit. This is normal and does not affect the usage of Steamworks.NET.

See this [Github issue](https://github.com/rlabrecque/Steamworks.NET/issues/13) for common causes and fixes.

### The Steam Overlay doesn't work!

For the Steam Overlay to work it must be injected before the renderer (DirectX/OpenGL) gets initialized. This is not feasible from Unity as Scripts only start running after the renderer is set up and injection is done through the `SteamAPI.Init()` API call.

It should work fine when launched through Steam as Steam will always inject the overlay into all processes as it starts.

It is highly recommended that you set your AppId in `SteamAPI.RestartAppIfNecessary()` which can be found in SteamManager. It will relaunch your application if it is launched from the exe directly, giving you the overlay all the time.

### The Steam Overlay causes rendering errors!

There are many cases where the overlay does not play nice with Unity, unfortunately this happens regardless of using Steamworks.NET and we are unable to resolve Steam Overlay issues as it is out of our control.

If you are running into overlay issues please check out [this](http://steamcommunity.com/groups/steamworks/discussions/0/540744936523837631/) Steamworks Discussions post.

### What languages does Steamworks.NET support?

In theory it should work with any .NET based language! Documentation is only provided for C#, but many people have used it from UnityScript as well. Let me know if you're using it from Boo or F#!

### My Unity build doesn't launch!

You are likely missing the steam_appid.txt from your output folder. Steamworks.NET does not copy it over automatically because it is not something that you should be shipping to customers.

### Steam only shows me playing 'SpaceWar' how do I change it to my game?

When you launch your game from outside of Steam it relies on steam_appid.txt to be present to let steam know what is being launched. To change from SpaceWar to your own game you must edit the steam_appid.txt which is found in the root of your Unity project, or next to your built .exe/.app. Replace '480' with your Valve provided AppId.

### Does the Steam DRM wrapper work with Unity or XNA/Monogame?

No it doesn't. Unfortunately the Steam DRM wrapper does not play well with .NET applications such as Unity and your application will likely fail to start if it's wrapped. Even if it does succeed and work for you it will often fail on other computers.

### The achievement popup only occurs after I quit my game.

This is most likely either caused by not calling `SteamAPI.RunCallbacks()` at regular intervals in your game or not calling `SteamUserStats.StoreStats()` after you set an achievement.

### Can I disable compilation of Steamworks.NET on specific platforms?

Yes! Just define `DISABLESTEAMWORKS`. This can be done with Unity in the Player Settings. For more information see [Unity's Documentation](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html)
Note that you will have to wrap all your code that calls Steamworks functions in `#if` statements as well.

### What versions of Unity are supported?

Steamworks.NET currently targets Unity 4.7 and 5.0+. Unity Pro is required when using 4.7.