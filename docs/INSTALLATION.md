# Installing Gemfields Claim Viewer v0.5.18

## System requirements

- Windows 10 version 1809 or later, or Windows 11
- 64-bit compatible Windows computer
- Approximately 200 MB of free space for installation and local working data
- Internet connection for live searches, updates, imagery, roads, aerial catalogue lookups and optional zone overlays

The installer contains the required .NET 9 desktop runtime. No separate .NET installation is needed.

## Install

1. Open the [v0.5.18 release](https://github.com/joshg524/Gemfields-Claim-Viewer-Releases/releases/tag/v0.5.18) and download [`GemfieldsClaimViewer-Setup-v0.5.18-win-x64.exe`](https://github.com/joshg524/Gemfields-Claim-Viewer-Releases/releases/download/v0.5.18/GemfieldsClaimViewer-Setup-v0.5.18-win-x64.exe) under **Assets**.
2. Verify its SHA-256 checksum:

   `17A3000386D38E3ED21CB45E0F06B62C20DC481CBD39773A00A115FD001F161A`

3. Run the installer.
4. Review the Windows warning carefully. This release is unsigned and can appear as an unknown publisher.
5. Leave **Create a desktop shortcut** selected if a desktop shortcut is wanted.
6. Complete installation and open Gemfields Claim Viewer from the Start menu or desktop shortcut.
7. The application opens maximized with current MC/ML boundaries and public imagery when available. Browse the map or enter your own MC or ML authority number. The grey `MC or ML 00000` text is an example.

The application installs for the current Windows account and does not request administrator rights.

## Local data

Saved claims, notes, caches and the refreshed claims library are kept under:

`%LOCALAPPDATA%\QueenslandMiningClaimResearch`

Installing, upgrading and uninstalling retain this folder. Back it up separately if the saved research is important. Delete it manually only when intentionally removing that local data.

## Uninstall

Open **Windows Settings → Apps**, find **Gemfields Claim Viewer**, and choose **Uninstall**.

## First-use reminder

The bundled claims library is a dated, bounded snapshot. Use **UPDATE CLAIMS DATA** to attempt a complete validated refresh, and verify legal status and access through official Queensland Government channels before relying on any result.

