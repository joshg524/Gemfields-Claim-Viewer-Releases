# Installing Gemfields Claim Viewer v0.5.14

## System requirements

- Windows 10 version 1809 or later, or Windows 11
- 64-bit compatible Windows computer
- Approximately 200 MB of free space for installation and local working data
- Internet connection for live searches, updates, imagery, roads, aerial catalogue lookups and optional zone overlays

The installer contains the required .NET 9 desktop runtime. No separate .NET installation is needed.

## Install

1. Download `GemfieldsClaimViewer-Setup-v0.5.14-win-x64.exe` from the official GitHub Release.
2. Verify its SHA-256 checksum:

   `E2443D2E59D0EE35B3E3BAF7C16C621CC4633AF395DD150CCB0BEFA1A8B5E756`

3. Run the installer.
4. Review the Windows warning carefully. This release is unsigned and can appear as an unknown publisher.
5. Leave **Create a desktop shortcut** selected if a desktop shortcut is wanted.
6. Complete installation and open Gemfields Claim Viewer from the Start menu or desktop shortcut.
7. The application opens without selecting a claim. Enter your own MC or ML authority number to begin; the grey `MC or ML 00000` text is only an example placeholder.

The application installs for the current Windows account and does not request administrator rights.

## Local data

Saved claims, notes, caches and the refreshed claims library are kept under:

`%LOCALAPPDATA%\QueenslandMiningClaimResearch`

Installing, upgrading and uninstalling retain this folder. Back it up separately if the saved research is important. Delete it manually only when intentionally removing that local data.

## Uninstall

Open **Windows Settings → Apps**, find **Gemfields Claim Viewer**, and choose **Uninstall**.

## First-use reminder

The bundled claims library is a dated, bounded snapshot. Use **UPDATE CLAIMS DATA** to attempt a complete validated refresh, and verify legal status and access through official Queensland Government channels before relying on any result.

