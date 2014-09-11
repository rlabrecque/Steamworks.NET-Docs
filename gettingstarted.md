---
title: Getting Started
layout: default
---
## Getting Started with Steamworks.NET

After completing the [Installation](/installation/) steps you can start using Steamworks.NET in your project.

The SteamManager script provides a great starting point for your project and I would highly recommend utilizing it in your project as it will signifigently cut down on the time required to get up and running.

If you are using Steamworks.NET from the .unitypackage you can import it directly into your project. Otherwise if you have cloned Steamworks.NET from Github you must download the [SteamManager](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) script manually.

Once the SteamManager script is in your project simply create a new empty GameObject in your first scene and add the SteamManager script to it.

You can now be able to launch your game and Steam should show you in-game.

I would then recommend trying out an easy SteamAPI method call.

We'll create a new script called `SteamScript.cs`:

First we'll add using Steamworks; to the file,

<pre><code>using UnityEngine;
using System.Collections;
using Steamworks;</code></pre>

Next we'll add our function call which gets the display name of the Steam user like so.


<pre><code>public class SteamScript : MonoBehaviour {
	void Start() {
		if(SteamManager.Initialized) {
			string name = SteamFriends.GetPersonaName();
			Debug.Log(name);
		}
	}
}</code></pre>

Note that we ALWAYS ensure that Steam is initalized by checking SteamManager.Initialized before calling any Steamworks functions.

Now just add the script to a GameObject and try it out!

If you run into any issues have a look at the [Frequently Asked Questions](/faq/) to see if it's covered in there!

You are successfully using Steamworks!

### Steam Callbacks

Callbacks are an important feature of Steamworks, they allow you to retrieve data asynchronously from Steam without locking up your game.

One such Callback that you will likely wish to utilize is called `GameOverlayActivated_t`. As the name implies it sends you a callback every time the Steam Overlay is activated or deactivated.

To use it we will continue with our script from earlier.

To use Callbacks in Steamworks.NET you first declare a protected Callback&lt;&gt; at class scope as a member variable to old onto the Callback.

<pre><code>public class SteamScript : MonoBehaviour {
	protected Callback&lt;GameOverlayActivated_t&gt; m_GameOverlayActivated;
}</code></pre>

We then create our Callback by calling Callback&lt;&gt;.Create() and assigning it to m_GameOverLayActivated.

We do this in OnEnable as that allows us to recreate the Callback after Unity does an assembly reload.

<pre><code>public class SteamScript : MonoBehaviour {
	protected Callback&lt;GameOverlayActivated_t&gt; m_GameOverlayActivated;

	private void OnEnable() {
		if (SteamManager.Initialized) {
			m_GameOverlayActivated = Callback&lt;GameOverlayActivated_t&gt;.Create(OnGameOverlayActivated);
		}
	}
}</code></pre>


The last piece of the puzzle is the OnGameOverlayActivated function.

<pre><code>public class SteamScript : MonoBehaviour {
	protected Callback&lt;GameOverlayActivated_t&gt; m_GameOverlayActivated;

	private void OnEnable() {
		if (SteamManager.Initialized) {
			m_GameOverlayActivated = Callback&lt;GameOverlayActivated_t&gt;.Create(OnGameOverlayActivated);
		}
	}

	private void OnGameOverlayActivated(GameOverlayActivated_t pCallback) {
		if(pCallback.m_bActive) {
			Debug.Log("Steam Overlay has been activated");
		}
		else {
			Debug.Log("Steam Overlay has been closed");
		}
	}
}</code></pre>


And that's it!

One popular use case for the GameOverlayActivated Callback is to pause the game when the overlay opens.

### Steam CallResults

There is one last aspect that this guide will cover and that is CallResults.

CallResults are very similar to Callbacks but they are the asyncronous result of a specific function call rather than a global event sink like Callbacks.

You can identify a function that provides a CallResult by inspecting it's return value. If it returns `SteamAPICall_t` then you must set up a CallResult.

In Steamworks.NET setting up a CallResult is nearly identical to Callbacks!

First we declare a CallResult&lt;&gt; at class scope as a member variable.

<pre><code>public class SteamScript : MonoBehaviour {
	private CallResult&lt;NumberOfCurrentPlayers_t&gt; m_NumberOfCurrentPlayers;
}</code></pre>

Next we can create our CallResult with `CallResult&lt;&gt;.Create`. Once again we do this in OnEnable so that it gets recreated every time Unity reloads the assembilies.

<pre><code>public class SteamScript : MonoBehaviour {
	private CallResult&lt;NumberOfCurrentPlayers_t&gt; m_NumberOfCurrentPlayers;

	private void OnEnable() {
		if (SteamManager.Initialized) {
			m_NumberOfCurrentPlayers = CallResult&lt;NumberOfCurrentPlayers_t&gt;.Create(OnNumberOfCurrentPlayers);
		}
	}
}</code></pre>

Once again, we create our function which gets called asyncronously.

The function signature of a CallResult is slightly different than a Callback with the addition of `bool bIOFailure`.

<pre><code>public class SteamScript : MonoBehaviour {
	private CallResult&lt;NumberOfCurrentPlayers_t&gt; m_NumberOfCurrentPlayers;

	private void OnEnable() {
		if (SteamManager.Initialized) {
			m_NumberOfCurrentPlayers = CallResult&lt;NumberOfCurrentPlayers_t&gt;.Create(OnNumberOfCurrentPlayers);
		}
	}

	private void OnNumberOfCurrentPlayers(NumberOfCurrentPlayers_t pCallback, bool bIOFailure) {
		if (pCallback.m_bSuccess != 1 || bIOFailure) {
			Debug.Log("There was an error retrieving the NumberOfCurrentPlayers.");
		}
		else {
			Debug.Log("The number of players playing your game: " + pCallback.m_cPlayers);
		}
	}
}</code></pre>

Finally the last thing that we need is to call a function which returns the CallResult and then associate the SteamAPICall_t handle which it returns with our CallResult.

<pre><code>public class SteamScript : MonoBehaviour {
	private CallResult&lt;NumberOfCurrentPlayers_t&gt; m_NumberOfCurrentPlayers;

	private void OnEnable() {
		if (SteamManager.Initialized) {
			m_NumberOfCurrentPlayers = CallResult&lt;NumberOfCurrentPlayers_t&gt;.Create(OnNumberOfCurrentPlayers);
		}
	}

	private void Update() {
		if(Input.GetKeyDown(KeyCode.Space)) {
			SteamAPICall_t handle = SteamUserStats.GetNumberOfCurrentPlayers();
			m_NumberOfCurrentPlayers.Set(handle);
			Debug.Log("Called GetNumberOfCurrentPlayers()");
		}
	}

	private void OnNumberOfCurrentPlayers(NumberOfCurrentPlayers_t pCallback, bool bIOFailure) {
		if (pCallback.m_bSuccess != 1 || bIOFailure) {
			Debug.Log("There was an error retrieving the NumberOfCurrentPlayers.");
		}
		else {
			Debug.Log("The number of players playing your game: " + pCallback.m_cPlayers);
		}
	}
}</code></pre>

And that's it for CallResults!

Now that you have familiarized yourself with the building blocks of Steamworks check out the [Steamworks.NET Example](https://github.com/rlabrecque/) application to see an industrial strength implementation of Steamworks Stats and Achievements!

## How the SteamManager script works

You will likely wish to make changes to SteamManager yourself and understanding why it does what it does is an important step to fully mastering Steamworks.

### Boilerplate

All the below code is wrapped within a MonoBehavior class so that it can be added to a GameObject.

<pre><code>using UnityEngine;
using System.Collections;
using Steamworks;

class SteamManager : MonoBehaviour {

}</code></pre>

### Persistent GameObject/Singleton logic

The SteamManager script relies on being created once and persisting throughout the duration of the game. This involves some rather heavy logic to integrate with Unity's GameObject system.

We use a "Self-Creating Persistent Singleton" to accomplish this. You can read more about singletons in the wonderful [UnityPatterns](http://unitypatterns.com/singletons/) guidebook.

With this pattern you can use the SteamManager from any scene in your game without having to manually place a SteamManager GameObject in each one. As usual refrain from interacting with the SteamManager from Awake() or OnDestroy() in other scripts as the execution order is not garenteed.

If you already have a method of maintaining global state in your game you may wish to replace this with your own method.

<pre><code>private static SteamManager s_instance;
private static SteamManager Instance {
	get {
		return m_instance ?? new GameObject("SteamManager").AddComponent<SteamManager>();
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

`DllCheck.Test()` checks to make sure the Steamworks redistributable binaries are the correct version. This is particularily useful when you upgrade Steamworks.NET, Especially if you are not using the Steamworks.NET editor scripts. Running Steamworks.NET with the wrong steam_api.dll can potentially have problamatic consequences.

<pre><code>if (!Packsize.Test()) {
	Debug.LogError("[Steamworks.NET] Packsize Test returned false, the wrong version of Steamworks.NET is being run in this platform.", this);
}

if (!DllCheck.Test()) {
	Debug.LogError("[Steamworks.NET] DllCheck Test returned false, One or more of the Steamworks binaries seems to be the wrong version.", this);
}</code></pre>

### SteamAPI.RestartAppIfNecessary

The first Steamworks function call which the script makes is `SteamAPI.RestartAppIfNecessary((AppId)480)`. Replace 480 with your own AppId.

SteamAPI.RestartAppIfNecessary() detects if the Steam client is running, if Steam is not running or the game wasn't started through Steam. It will return true if a Restart is needed. If it returns true it starts the Steam client if required and launches your game again through it, you should then manually close your application as quickly as possible.

If it returns false, your game was launched by the Steam client and execution continues as normal.

If the steam_appid.txt file is present in the current working directory then SteamAPI_RestartAppIfNecessary() will return false.

Being that this is the first Steamworks function which gets called this is the ideal location to ensure that the steam_api.dll and CSteamworks.dll can be infact be loaded. This is accomplished by wrapping this function call in a try..catch block.

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

The second Steamworks function which should be called is to `SteamAPI.Init()`, This start's the SteamAPI and must be called before any other Steamworks functions are called.

If SteamAPI.Init() returns true then everything has been set up to proceede using Steamworks.NET

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

If you pause the game by setting Time.timeScale to 0 then Update() functions will no longer run. You want to look into alternatives to ensure that SteamAPI.RunCallbacks is running even when your game is paused. [Coroutines](http://docs.unity3d.com/Manual/Coroutines.html) may be a good option for you.

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