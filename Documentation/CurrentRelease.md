# Roam Control 0.9.6

Build 92, released 4 October 2026.

> [!IMPORTANT]
> **Upgrading from Roam Control 0.9.4? Back up before installing 0.9.6.**
>
> Roam Control 0.9.6 uses the new `com.roamcontrol` app identifier, so your existing 0.9.4 app data will **not automatically carry across**.
>
> Before installing 0.9.6, open Roam Control 0.9.4 and create a backup from **Settings > Backup & Restore** if you want to preserve your favourites, history and supported settings.
>
> After installing 0.9.6, choose **Restore Backup** during the migration flow.
>
> Pairing information is not included in backups, so this iPhone will need to be paired again.
>
> **If you install 0.9.6 without creating a backup first, Roam Control cannot automatically recover those items from the old app.**

Roam Control 0.9.6 carries forward the major 0.9.5 update and adds improved file-import compatibility for backup restores and GPX routes.

## What's fixed in 0.9.6

- Improved backup restore and backup import compatibility on some re-signed and paid-certificate installations.
- Improved GPX import compatibility in the same environments.
- Reworked file importing to use a copy-based document picker, avoiding cases where selecting a file could leave the Files picker open without returning to Roam Control.
- Build date metadata is now generated automatically for each actual app build instead of being manually carried between development builds.

## Before installing

- Requires iOS 27 or newer.
- Requires Developer Mode and LocalDevVPN.
- This is an unsigned IPA. SideStore signs it with the user's own Apple account.
- Intended only for development, quality assurance and responsible testing on a device the user owns and controls.

Read the [installation guide](Installation.md), [privacy explanation](Privacy.md) and [responsible-use policy](ResponsibleUse.md) before using it.

## Download

Download `Roam-Control-0.9.6-Build-92.ipa` from the [Roam Control 0.9.6 release](https://github.com/seanhowarthdev/Roam-Control/releases/tag/v0.9.6).

SHA-256:

`2844744c44fa81407c118db33fb48d4a6f5d1774caed2fa35dbed3e9625d6836`

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
- Improved backup and GPX file importing in 0.9.6.

## Migration from 0.9.4

Roam Control 0.9.6 uses the `com.roamcontrol` app identifier.

Create a backup in 0.9.4 before installing 0.9.6 if you want to preserve favourites, history and supported settings. Restore that backup during the migration flow.

Pairing information is deliberately excluded from backups, so the iPhone must be paired again.

See the [0.9.5 Migration & SideStore FAQ](Migration095.md) for App ID, active-app slot, expiry and backup questions. The same migration path applies when moving directly from 0.9.4 to 0.9.6.

## Distribution constraints

SideStore and free Apple accounts are subject to Apple's app-count and seven-day refresh limits. Pairing and location sessions require a physical iPhone.

Please report ordinary bugs with the issue template and security problems through a private GitHub security advisory.
