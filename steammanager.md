---
title: How the SteamManager script works
layout: default
---
The SteamManager script is what we consider the 'usermode' code. It provides some basic logic to setup and maintain the connection with Steam.

You will likely wish to make changes to the SteamManager script yourself and understanding why it does what it does is an important step to fully mastering Steamworks.

### Boilerplate

All of the code below is wrapped within a MonoBehavior class so that it can be added to a GameObject.

<pre><code>using UnityEngine;
using System.Collections;
using Steamworks;

class SteamManager : MonoBehaviour {

}</code></pre>

### Persistent GameObject/Singleton logic

The SteamManager script relies on being created once and persisting throughout the duration of the game. This involves some rather heavy logic to integrate with Unity's GameObject system.

We use a "Self-Creating Persistent Singleton" to accomplish this.

With this pattern you can use the SteamManager from any scene in your game without having to manually place a SteamManager GameObject in each one. As usual refrain from interacting with the SteamManager from Awake() or OnDestroy() in other scripts as the execution order is not guaranteed.

If you already have a method of maintaining global state in your game you may wish to replace this with your own method.

<pre><code>private static SteamManager s_instance;
private static SteamManager Instance {
	get {
		return m_instance ?? new GameObject("SteamManager").AddComponent&lt;SteamManager&gt;();
	}
}

private void Awake() {
	if (s_instance != null) {
		Destroy(gameObject);
		return;
	}
	s_instance = this;

	DontDestroyOnLoad(gameObject);
}

private void OnEnable() {
	if (s_instance == null) {
		s_instance = this;
	}
}

private void OnDestroy() {
	if (m_instance != this) {
		return;
	}
	s_instance = null;
}</code></pre>

### Sanity Checks

Steamworks.NET provides a couple non-essential sanity checks to ensure that Steamworks.NET is being used correctly.

`Packsize.Test()` ensures that Steamworks.NET is running under the correct platform, In Unity under normal operation this should never return false.

`DllCheck.Test()` checks to make sure the Steamworks redistributable binaries are the correct version. This is particularly useful when you upgrade Steamworks.NET, Especially if you are not using the Steamworks.NET editor scripts. Running Steamworks.NET with the wrong steam_api.dll will likely result in issues. (Currently only checks steam_api[64].dll)

<pre><code>if (!Packsize.Test()) {
	Debug.LogError("[Steamworks.NET] Packsize Test returned false, the wrong version of Steamworks.NET is being run in this platform.", this);
}

if (!DllCheck.Test()) {
	Debug.LogError("[Steamworks.NET] DllCheck Test returned false, One or more of the Steamworks binaries seems to be the wrong version.", this);
}</code></pre>

### SteamAPI.RestartAppIfNecessary

The first Steamworks function call which the script makes is `SteamAPI.RestartAppIfNecessary((AppId)480)`. Replace 480 with your own AppId.

SteamAPI.RestartAppIfNecessary() checks if the Steam client is running, and will start it if it's not.

If it returns true it starts the Steam client if required and launches your game again through it, you should then manually close your application as quickly as possible. This effectively runs `steam://run/[AppId]` so it may not relaunch the exact executable that called it.

If it returns false, your game was launched by the Steam client and execution continues as normal.

If the steam_appid.txt file is present in the current working directory then SteamAPI_RestartAppIfNecessary() will return false. This allows you to develop without relaunching through Steam every time.

Being that this is the first Steamworks function which gets called it is the ideal location to ensure that the steam_api.dll and CSteamworks.dll can infact be loaded. This is accomplished by wrapping this function call in a try..catch block to catch DllNotFoundException.

<pre><code>private void Awake() {
	try {
		if (SteamAPI.RestartAppIfNecessary((AppId)480)) {
				Application.Quit();
				return;
			}
		}
	catch (System.DllNotFoundException e) {
		Debug.LogError("[Steamworks.NET] Could not load [lib]steam_api.dll/so/dylib. It's likely not in the correct location. Refer to the README for more details.\n" + e, this);

		Application.Quit();
		return;
	}
}</code></pre>

### SteamAPI.Init

The second Steamworks function which should be called is to `SteamAPI.Init()`, this starts the SteamAPI and must be called before any other Steamworks functions are called.

If SteamAPI.Init() returns true then everything has been set up to proceed using Steamworks.NET

Otherwise a return value of false is caused by one of three issues.

 * The Steam Client is not running. A running Steam client is required to provide implementations of the various Steamworks interfaces.
 * The Steam client couldn't determine the AppID of game. Make sure you have steam_appid.txt in your game directory. This should never happen when launching your game when downloaded through Steam, as SteamAPI.RestartAppIfNecessary() will relaunch it through Steam.
 * Ensure that your application is running under the same user context as the Steam client, including admin privledges.

If you're running into Init issues try running Microsoft's [DbgView](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx) prior to launching to get the internal output from Steam.

SteamManager exposes the Initialized property publicly which you can use from other scripts to ensure that the SteamAPI is initialized before calling any Steamworks function.

<pre><code>private bool m_bInitialized;
public static bool Initialized {
	get {
		return Instance.m_bInitialized;
	}
}

private void Awake() {
	m_bInitialized = SteamAPI.Init();
	if (!m_bInitialized) {
		Debug.LogError("[Steamworks.NET] SteamAPI_Init() failed. Refer to Valve's documentation or the comment above this line for more information.", this);

		return;
	}
}</code></pre>

### SteamAPIWarningMessageHook

By calling SteamClient.SetWarningMessageHook() with a function delegate we can intercept warning messages from Steam under certain situations.

Note that we ensure that the Steam API is initialized before calling any Steamworks functions.

We call this in OnEnable so that it gets recreated after Unity does an Assembly Reload such as when recompiling scripts.

To recieve warning messages from Steam you must launch your game with "-debug_steamapi" in the launch arguments.

<pre><code>private SteamAPIWarningMessageHook_t m_SteamAPIWarningMessageHook;
private static void SteamAPIDebugTextHook(int nSeverity, System.Text.StringBuilder pchDebugText) {
	Debug.LogWarning(pchDebugText);
}

private void OnEnable() {
	if (!m_bInitialized) {
		return;
	}

	if (m_SteamAPIWarningMessageHook == null) {
		m_SteamAPIWarningMessageHook = new SteamAPIWarningMessageHook_t(SteamAPIDebugTextHook);
		SteamClient.SetWarningMessageHook(m_SteamAPIWarningMessageHook);
	}
}</code></pre>

### SteamAPI.RunCallbacks

For the Callback and CallResult systems to dispatch events `SteamAPI.RunCallbacks` must be called frequently. The more time between calls, the more potential latency between receiving events or results from the Steam API.

If you pause the game by setting Time.timeScale to 0 then Update() functions will no longer run. You will want to look into alternatives to ensure that SteamAPI.RunCallbacks is running even when your game is paused. [Coroutines](http://docs.unity3d.com/Manual/Coroutines.html) may be a good option for you.

Note that we ensure that the Steam API is initialized before calling any Steamworks functions.

<pre><code>private void Update() {
	if (!m_bInitialized) {
		return;
	}

	// Run Steam client callbacks
	SteamAPI.RunCallbacks();
}</code></pre>

### SteamAPI.Shutdown

The final call that SteamManager will make is to `SteamAPI.Shutdown` which cleans up the SteamAPI and lets steam know that you are preparing to shutdown.

OnDestroy is used as it's the very last thing that gets called upon shutdown.

Because the SteamManager should be persistent and never disabled or destroyed we can use OnDestroy to shutdown the SteamAPI.

<pre><code>private void OnDestroy() {
	if (!m_bInitialized) {
		return;
	}

	SteamAPI.Shutdown();
}</code></pre>