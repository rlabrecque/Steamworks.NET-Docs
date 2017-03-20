---
title: Code Snippits
layout: default
---
These are minimal examples to help you get started utilizing Steamworks.NET. They should provide a strong base on which you can build upon.

## SteamFriends

### How do I get the players display name?
<pre><code>string name = SteamFriends.GetPersonaName();
Debug.Log(name);</code></pre>

### How do I loop through the list of friends and get their name and status?

<pre><code>int friendCount = SteamFriends.GetFriendCount(EFriendFlags.k_EFriendFlagImmediate);
Debug.Log("[STEAM-FRIENDS] Listing " + friendCount + " Friends.");
for (int i = 0; i &lt; friendCount; ++i) {
	CSteamID friendSteamId = SteamFriends.GetFriendByIndex(i, EFriendFlags.k_EFriendFlagImmediate);
	string friendName = SteamFriends.GetFriendPersonaName(friendSteamId);
	EPersonaState friendState = SteamFriends.GetFriendPersonaState(friendSteamId);

	Debug.Log(friendName + " is " + friendState);
}</code></pre>


## SteamUser

### How do I get the players unique SteamID?
<pre><code>SteamID steamId = SteamUser.GetSteamID();
Debug.Log(steamId);</code></pre>
