# Roam Control 0.9.5

Build 91, released 3 October 2026.

> [!IMPORTANT]
> **Upgrading from Roam Control 0.9.4? Back up before installing 0.9.5.**
>
> Roam Control 0.9.5 uses a new app identifier, so your existing 0.9.4 app data will **not automatically carry across**.
>
> Before installing 0.9.5, open Roam Control 0.9.4 and create a backup from **Settings > Backup & Restore** if you want to preserve your favourites, history and supported settings.
>
> After installing 0.9.5, choose **Restore Backup** during the migration flow.
>
> Pairing information is not included in backups, so this iPhone will need to be paired again.
>
> **If you install 0.9.5 without creating a backup first, Roam Control cannot automatically recover those items from the old app.**


Roam Control 0.9.5 is a major update with a redesigned interface, Driving and Cycling modes, Joystick movement, smoother location simulation, GPX support, improved recovery and a dedicated migration path from 0.9.4.

## Before installing

- Requires iOS 27 or newer.
- Requires Developer Mode and LocalDevVPN.
- This is an unsigned IPA. SideStore signs it with the user's own Apple account.
- Intended only for development, quality assurance and responsible testing on a device the user owns and controls.

Read the [installation guide](Installation.md), [privacy explanation](Privacy.md) and [responsible-use policy](ResponsibleUse.md) before using it.

## Download

Download `Roam-Control-0.9.5-Build-91.ipa` from the [Roam Control 0.9.5 release](https://github.com/seanhowarthdev/Roam-Control/releases/tag/v0.9.5).

SHA-256:

`c22bcb0998cb90e4c9e4f8c5fdd5f4483170656c6f1e98ba35d9daf9cbec304f`

## Highlights

- Major visual redesign across the app.
- Added Walking, Driving and Cycling route modes.
- Added free movement with the new Joystick.
- Added smoother simulated movement and improved map following.
- Added GPX route import and export.
- Added Miles/Kilometres route units and mph/km/h speed display.
- Added the dedicated 0.9.4 migration and restore flow.
- Improved pairing readiness, expiry recovery and interrupted-session recovery.
- Redesigned Connection Health, Backup & Restore and Privacy Details.
- Added the documented anonymous country-level install count.
- Fixed future semantic-version update handling.

## Migration from 0.9.4

Roam Control 0.9.5 uses the new `com.roamcontrol` app identifier.

Create a backup in 0.9.4 before installing 0.9.5 if you want to preserve favourites, history and supported settings. Restore that backup during the 0.9.5 migration flow.

Pairing information is deliberately excluded from backups, so the iPhone must be paired again.

See the [0.9.5 Migration & SideStore FAQ](Migration095.md) for App ID, active-app slot, expiry and backup questions.

## Distribution constraints

SideStore and free Apple accounts are subject to Apple's app-count and seven-day refresh limits. Pairing and location sessions require a physical iPhone.

Please report ordinary bugs with the issue template and security problems through a private GitHub security advisory.
