# Roam Control 0.9.7

Build 96, released 8 October 2026.

> [!IMPORTANT]
> **Upgrading directly from Roam Control 0.9.4? Back up before installing 0.9.7.**
>
> Roam Control 0.9.7 uses the newer `com.roamcontrol` app identifier. Existing 0.9.4 app data will not automatically transfer.
>
> Before upgrading, create a backup in 0.9.4 under **Settings > Backup & Restore**.
>
> Restore the backup during the migration flow. Pairing information is not included, so you must pair again.

## What's improved

Roam Control 0.9.7 is a maintenance update focused on connection reliability.

- Improved background location-session connection handling.
- Improved mobile-data reliability when LocalDevVPN is active.
- Corrected native network-interface selection when LocalDevVPN and a separate Personal VPN are connected simultaneously.
- Tested fixed locations, Walking, Driving, Cycling and LocalDevVPN reconnection.

These improvements were validated on the developer's device. Compatibility with every VPN configuration is not guaranteed.

## Before installing

- Requires iOS 27 or newer.
- Requires Developer Mode and LocalDevVPN.
- Distributed as an unsigned IPA for signing through SideStore.
- Intended for development, quality assurance and responsible testing on devices you own or control.

Read the [installation guide](Installation.md), [privacy explanation](Privacy.md) and [responsible-use policy](ResponsibleUse.md).

## Download

Download `RoamControl-0.9.7-Build96.ipa` from the [Roam Control 0.9.7 release](https://github.com/seanhowarthdev/Roam-Control/releases/tag/v0.9.7).

SHA-256:

`8236b2005d96219ba6d00d6a1c9d9af08792ae32f3ea1e997662268df4a908d5`

## Existing features

All features from Roam Control 0.9.6 remain available, including fixed locations, Walking, Driving and Cycling routes, Joystick movement, GPX support, favourites, history, pairing and location restoration.

## Migration from 0.9.4

See the [0.9.5 Migration & SideStore FAQ](Migration095.md). The same migration requirements apply when upgrading directly from 0.9.4 to 0.9.7.

## Distribution constraints

SideStore and free Apple accounts remain subject to Apple's app-count and seven-day refresh limits. Pairing and location sessions require a physical iPhone.

Report ordinary bugs through GitHub Issues and security problems through a private security advisory.
