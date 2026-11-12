# The Bus – AMP Templates for the Dedicated Server

AMP (CubeCoders) game templates for the **The Bus Dedicated Server** on Linux.
The server is a Windows application and runs under AMP via Wine.
Public (Stable) and Beta are two separate templates that can coexist on the same host.

Author: **Ehrenecke** · Unofficial, not affiliated with CubeCoders or the developers of The Bus.

## What the templates do

| Template in AMP | Steam branch | Installation folder |
|---|---|---|
| Custom / The Bus | `public` (Stable) | `./the-bus/` |
| Custom / The Bus (Beta) | `beta` | `./the-bus-beta/` |

- **Installation and updates:** via SteamCMD, Steam app **507320** (The Bus – Dedicated Server, free, anonymous login). The Windows depot is downloaded. The parent app is 491540 (the game).
- **Branch:** Each template has its branch hard-coded. There is no switcher; instead, you choose the matching template when creating the instance.
- **Start:** `wine "./TheBus/Binaries/Win64/TheBusServer.exe"` using the AMP default Wine image `cubecoders/ampbase:wine`. Neither Xvfb nor a virtual display is required.
- **Startup parameters:** `-log -useperfthreads -port=<Game Port> -QueryPort=<Query Port>`, plus optional custom additional parameters.
- **Wine:** The prefix is located at `<instance>/.wine` (64-bit). Wine creates it automatically on first start, which takes a little longer the first time. `WINEDLLOVERRIDES=mscoree,mshtml=` prevents Mono/Gecko installation dialogs.
- **Console and status:** AMP reads the Unreal log file `TheBus/Saved/Logs/TheBus.log` (TailLogFile, as in the official Farming Simulator template). The status is "Ready" immediately after start. Whether the server is really running is shown by the line `Server can be reached with the GUID` in the console.
- **Stopping:** `SIGTERM`, timeout 30 s. This terminates the server forcefully. If a stop hangs, use **Kill** in AMP.
- **Directories:** During the update, `TheBus/Saved/Config/WindowsServer/` and `TheBus/Saved/Logs/` are created.

## Ports

| Port | Protocol | Purpose |
|---|---|---|
| 7777 | UDP | Game Port |
| 27015 | UDP | Query Port |

Open both ports in your firewall. No further inbound ports are documented. The connection to the server is made via GUID over Epic Online Services (outbound).

## Settings in AMP

- **Additional startup parameters:** free-form Unreal parameters that are appended to `TheBusServer.exe`.
- **Config files:** The standard Unreal files `GameUserSettings.ini`, `Game.ini` and `Engine.ini` from `TheBus/Saved/Config/WindowsServer/` appear in the AMP configuration manager once the server has generated them. It is not confirmed that The Bus stores its own settings there.
- **Server name, passwords, max players:** according to the documentation, set via the in-game admin menu or server commands, not via startup parameters. The displayed player count (10) is only a default value and is not passed to the server.

## Files

| File | Purpose |
|---|---|
| `manifest.json` | Repository description, required by AMP |
| `thebus.kvp` | Public template |
| `thebusconfig.json`, `thebusmetaconfig.json`, `thebusports.json`, `thebusupdates.json` | Settings, config files, ports, updates (Public) |
| `thebus-beta.kvp` | Beta template |
| `thebus-betaconfig.json`, `thebus-betametaconfig.json`, `thebus-betaports.json`, `thebus-betaupdates.json` | Same for Beta |

All files must be lowercase and located in the repository root, without subfolders.

## Installation in AMP

1. In AMP: instance **ADS01** → *Configuration* → *Instance Deployment* → *Configuration Repositories* → **Add**:
   ```
   Ehrenecke/TheBus:main
   ```
2. Click **Fetch Latest**, then reload the browser (Ctrl+F5).
3. *Create Instance* → choose "Custom / The Bus" or "Custom / The Bus (Beta)".
4. After the instance is created, click **Update** (downloads approx. 3.8 GB), then start it.

Later changes to the template are not applied to instances that have already been created. Recreate the instance.

## Known limitations

- App 507320 also has a Linux depot (507322). The templates deliberately use the Windows depot with Wine.
- The name of the log file (`TheBus.log`) is assumed. If it is named differently, adjust `App.TailLogFilePath` in the `.kvp` files.
- `-QueryPort` is a standard Unreal parameter. Whether The Bus evaluates it is not documented.
- The server can only be terminated forcefully (SIGTERM). Whether it still saves cleanly in that case is untested.
- The templates are designed for x86_64 hosts.
