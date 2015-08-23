---
title: Getting Started
layout: default
---
## Getting Started with Steamworks.NET

After completing the [Installation](/installation/) steps you can start using Steamworks.NET in your project.

The SteamManager script provides the ideal starting point for your project and I would highly recommend utilizing it as it will significantly cut down on the time required to get up and running.

If you are using Steamworks.NET from the .unitypackage you can import it directly into your project. Otherwise if you have cloned Steamworks.NET from Github you must download the [SteamManager.cs](https://raw.githubusercontent.com/rlabrecque/SteamManager/master/SteamManager.cs) script manually.

Once the SteamManager script is in your project simply create a new empty GameObject in your first scene and add the SteamManager script to it.

You will now be able to launch your game and Steam should show you in-game.

---

Next, once you have that working I would then recommend trying out an easy SteamAPI method call.

We'll create a new script called `SteamScript.cs`:

First we'll add `using Steamworks;` to the file.

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

Callbacks are the most important aspect of Steamworks, they allow you to retrieve data asynchronously from Steam without locking up your game.

One such Callback that you will likely wish to utilize is `GameOverlayActivated_t`. As the name implies it sends you a callback every time the Steam Overlay is activated or deactivated.

To use it we will continue with our script from earlier.

To use Callbacks in Steamworks.NET you must first declare a `protected Callback<>` at class scope as a member variable to hold onto the Callback.

<pre><code>public class SteamScript : MonoBehaviour {
	protected Callback&lt;GameOverlayActivated_t&gt; m_GameOverlayActivated;
}</code></pre>

We then create our Callback by calling `Callback<>.Create()` and assigning it to m_GameOverLayActivated. This prevents the callback from being garbage collected.

We usually do this in OnEnable as that allows us to recreate the Callback after Unity reloads assemblies.

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
		if(pCallback.m_bActive != 0) {
			Debug.Log("Steam Overlay has been activated");
		}
		else {
			Debug.Log("Steam Overlay has been closed");
		}
	}
}</code></pre>


And that's it!

One popular and recommended use case for the GameOverlayActivated Callback is to pause the game when the overlay opens.

### Steam CallResults

There is one last aspect that this guide will cover and that is CallResults.

CallResults are very similar to Callbacks but they are the asyncronous result of a specific function call rather than a global event sink like Callbacks.

You can identify a function that provides a CallResult by inspecting it's return value. If it returns `SteamAPICall_t` then you must set up a CallResult.

In Steamworks.NET setting up a CallResult is nearly identical to Callbacks!

First we declare a `CallResult<>` at class scope as a member variable.

<pre><code>public class SteamScript : MonoBehaviour {
	private CallResult&lt;NumberOfCurrentPlayers_t&gt; m_NumberOfCurrentPlayers;
}</code></pre>

Next we can create our CallResult with `CallResult<>.Create`. Once again we do this in OnEnable so that it gets recreated every time Unity reloads the assembilies.

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

For both Callbacks and CallResults you must be calling `SteamAPI.RunCallbacks` on a regular basis. [This is done in SteamManager](/steammanager/#steamapiruncallbacks).

Now that you have familiarized yourself with the building blocks of Steamworks check out the [Steamworks.NET Example](https://github.com/rlabrecque/Steamworks.NET-Example) application to see an industrial strength implementation of Steamworks Stats and Achievements!

The [Steamworks.NET-Test](https://github.com/rlabrecque/Steamworks.NET-Test) project can be extremely valuable to figure out how accomplish various things in Steamworks.NET.

It's also recommended that you have a look at Valve's own documentation on [Getting started with the Steamworks API](https://partner.steamgames.com/documentation/getting_started)!