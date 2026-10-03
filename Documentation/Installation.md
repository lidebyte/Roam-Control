# Installation

Roam Control is not distributed through the App Store or TestFlight. Public beta builds are supplied as unsigned IPA files for users to sign with their own Apple account.

## Requirements

- An iPhone running iOS 27 or newer.
- Developer Mode enabled under **Settings → Privacy & Security**.
- [LocalDevVPN](https://apps.apple.com/app/localdevvpn/id6755608044) installed on the iPhone.
- SideStore for a standalone install, or LiveContainer for a contained install.

## Install with SideStore

1. Download the IPA attached to the matching GitHub Release. Do not download an IPA from an untrusted mirror.
2. In SideStore, tap **+** and choose the downloaded IPA.
3. Allow SideStore to sign and install Roam Control with your Apple account.
4. Open Roam Control and complete its introduction and device-pairing flow.
5. Open LocalDevVPN and enable its local tunnel before starting a location.

With a free Apple Account, SideStore documents two separate signing limits: up to 3 active apps at one time, including SideStore, and up to 10 App IDs registered within a 7-day period. These are Apple signing limits, not Roam Control subscriptions.

For ordinary updates that keep the same app identifier, install the newer IPA over the existing copy rather than deleting the app first.

**Roam Control 0.9.5 is a one-time exception for users coming from 0.9.4.** Version 0.9.5 uses the new `com.roamcontrol` app identifier, so existing 0.9.4 app data does not automatically carry across. Before installing 0.9.5, create a backup in **Settings > Backup & Restore** if you want to preserve favourites, history and supported settings. Restore that backup during the 0.9.5 migration flow. Pairing must be completed again.

For questions about the old and new App IDs, active app slots, App ID expiry and restoring 0.9.4 data, see the [0.9.5 Migration & SideStore FAQ](Migration095.md).


## Install with LiveContainer

Roam Control can also run through LiveContainer.

1. Download the IPA attached to the matching GitHub Release.
2. Import Roam Control into LiveContainer.
3. **Before launching Roam Control for the first time**, enable LiveContainer's local-notification compatibility option for that Roam Control instance:

   **LiveContainer → Apps → long-press Roam Control → App Settings → Fix Local Notifications → On**

4. Launch Roam Control and continue through first-time setup and pairing normally.
5. Open LocalDevVPN and enable its local tunnel before starting a location.

With **Fix Local Notifications** enabled, the pairing notification is delivered through LiveContainer. Tapping it returns to the correct Roam Control instance so pairing can continue normally.

Because LiveContainer relays the notification, it may show LiveContainer, sandbox or container-style details rather than looking exactly like a notification from a standalone Roam Control install. This is specific to the LiveContainer environment. SideStore installs are unaffected.

## Verify a release

Each GitHub Release publishes the IPA's SHA-256 checksum. On a Mac, calculate the checksum of the IPA you downloaded:

```sh
shasum -a 256 RoamControl-0.9.2-Beta3-build53.ipa
```

Compare the result with the SHA-256 value shown on the matching GitHub Release before installing it.

See the [user guide](UserGuide.md) for pairing and everyday operation.
