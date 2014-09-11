---
title: Changelog
layout: default
---
# Release 5.0.0

* Feature: **Updated to Steamworks SDK 1.31.**
* Feature: Increased support for Unity 4.6 beta and 5.0 beta.
* Feature: Added the DllCheck class which can detect runtime steam_api.dll version issues.
* Feature: Wrapped all function comments in &lt;summary&gt; tags to display them in the visual studio tooltips.

* Fixed: Corrected two ISteamMusicRemote function arguments from 'IntPtr' to 'AppId_t[]'
* Fixed: Removed the readonly flag from copied files to play nicer with some version control systems. (Thanks @neophit)

---

# Release 4.0.0

* Feature: **Updated to Steamworks SDK 1.30.**

* Changed: In SteamUGC.GetQueryUGCResult's arguments changed 'ref' to 'out'.
* Changed: Redesigned SteamMatchmakingServers to provide a much better usage experience. This includes implementing gameserveritem_t, MatchMakingKeyValuePair_t, and the Steam Matchmaking Response Rules.

* Fixed: Corrected SteamAppList.GetInstalledApps()'s arguments from 'out AppId_t' to 'AppId_t[]'
* Fixed: Added missing SteamMatchmaking.AddFavoriteGame().
* Fixed: Corrected SteamNetworking.GetP2PSessionState()'s second argument from IntPtr to 'out P2PSessionState_t'.
* Fixed: A handful of IntPtr -> byte[] arguments in the following functions:

		* SteamUnifiedMessages.SendMethod()
		* SteamUnifiedMessages.GetMethodResponseData()
		* SteamUnifiedMessages.SendNotification()
		* SteamGameServer.SendUserConnectAndAuthenticate()
		* SteamGameServer.GetAuthSessionTicket()
		* SteamGameServer.BeginAuthSession()
		* SteamGameServer.HandleIncomingPacket()
		* SteamGameServer.GetNextOutgoingPacket()
		* SteamUser.InitiateGameConnection()

* Fixed: The following GameServer functions which have default arguments had the wrong codegen resulting in a crash. This is now resolved. (Thanks to Nelson Sexton of Unturned for the report!):

		* SteamGameServerUtils.GetEnteredGamepadTextInput()
		* SteamGameServerNetworking.SendP2PPacket()
		* SteamGameServerNetworking.IsP2PPacketAvailable()
		* SteamGameServerNetworking.ReadP2PPacket()

---

# Release 3.0.0

* Feature: **Updated to Steamworks SDK 1.29a.**
* Feature: Steamworks.NET now throws an exception if Steamworks is not Initialized when calling interface functions.
* Feature: We now throw an exception when interface functions are called on non-supported platforms. (Like Mobile)
* Feature: 64Bit OSX (Universal) is now supported!
* Feature: Included all of the Steamworks global functions in NativeMethods.
* Feature: Callbacks and CallResults can now be dispatched to multiple functions.

* Changed: Completely reworked the Callback/CallResult backends.¹
* Changed: CallResult::Set() can now take a delegate function. You are no longer required to set one when you create the CallResult. (Like the C++ API)
* Changed: Removed the Linux Launch Scripts as they are no longer needed!
* Changed: Statically linked to the Visual Studio runtime so the VS2010 redistributable is no longer required!

* Fixed: The second last argument in SteamUserStats.GetDownloadedLeaderboardEntry changed from out int to int[].
* Fixed: Resolved a memory corruption issue with UGC tags.
* Fixed: Some lesser used Callback structs were the wrong size on some platforms resulting in corrupted results.


* **¹ This required an API Change.**
* Upgrade Instructions:
	* Find all usages of "new Callback<>();" in your code.
	* Replace each use with "Callback<>.Create()".
	* Additionally you must now store the Callback<> returned by Create() so that it does not get Garbage-Collected.
	* This results in the following:

			class YourClass : MonoBehavior {
			    protected Callback<UserStatsReceived_t> m_UserStatsReceived;
			    void OnEnable() {
			        m_UserStatsReceived = Callback<UserStatsReceived_t>.Create(OnUserStatsReceived);
			    }
			    private void OnUserStatsReceived(UserStatsReceived_t pCallback) {
			    }
			}

---

# Release 2.1.0

* Improved the Steam types so that they are all Comparable and Castable
* Made SetWarningMessageHook easier to use
* Fixed Regression caused by marshalling structs as return values on Unity's Linux Mono
* 64 Bit support on both Linux and Windows (Steam does not provide a 64bit OSX dylib!)

---

# Release 2.0.0

Steamworks.NET 2.0.0 provides some rather large changes most notable being:

* Reorganized folder structure (If you were using gitmodules to pull Steamworks.NET into your Plugins/ folder then that will no longer work.) (This is due to the Editor scripts and Standalone project being included)
* All comments/documentation from the C++ headers are now replicated in C#.
* Included 3 Editor scripts that improve the ease of installation and ease of building.
* Created a standalone project for non-Unity based games.
* Many Improvements to the automatically generated wrapper.
* Included SteamGameServer_* free functions
* Updated Steamworks to 1.28
* Redesigned the CallResult API to end up even closer to the C++ API

---

# Release 1.1.0

* ?

---

# Release 1.0.1

* ?

---

# Release 1.0.0

* Initial Release