# WinPE Toolkit

WinPE Toolkit is a PowerShell GUI for building and customizing WinPE media. It helps you create a reusable recovery and deployment environment with drivers, utilities, network support, and your own scripts, all from one interface.

The project is centered on [`Start-WinPEToolkitGui.ps1`](./Start-WinPEToolkitGui.ps1).

## What It Does

- opens and updates the WinPE boot image
- adds optional WinPE components
- includes tools like TightVNC, Explorer++, BGInfo, BitLocker helpers, and custom scripts
- adds storage and network drivers for recovery scenarios
- stages extra network files, including optional Wi-Fi profiles
- builds a bootable UEFI WinPE ISO

## Current Version

`1.0.1` as of June 13, 2026

### Recent Updates

- Initial build

## Requirements

- Windows PowerShell 5.1
- Administrator rights
- Windows ADK Deployment Tools
- Windows ADK WinPE add-on
- A desktop session that can show the GUI

The toolkit can automatically detect Windows ADK tools:

- `copype.cmd`
- `oscdimg.exe`

## Quick Start

1. Launch the GUI as an Administrator:

```powershell
powershell.exe -STA -File .\Start-WinPEToolkitGui.ps1
```

2. Confirm the detected ADK paths.
3. Choose whether to create a new WinPE workspace or reuse an existing one.
4. Select the features and packages you want to include.
5. Run the build workflow and create the ISO.

The combined `Run` action can move through the full process from start to finish, or you can turn individual steps on and off as needed.

## Features

The `Features` tab controls what gets added to the mounted image.

### TightVNC

Stages TightVNC from `Features\TightVNC` and prepares it for use inside WinPE.

### Explorer++

Stages Explorer++ from `Features\Explorer++` and adds a launcher in the WinPE menu.

### BGInfo

Stages BGInfo from `Features\BGInfo` and can start it automatically in WinPE. If `BGInfo.bgi` is present, that saved profile is used.

### Drivers

Scans `Features\Drivers` for `.inf` files and injects them into `boot.wim`. This is useful for storage, USB, or network hardware that WinPE may need during boot or recovery.

The WinPE menu can also live-load staged drivers with `drvload`.

### BitLocker Unlock

Stages the BitLocker unlock helper from `Features\Menu\Start-WinPEToolkit-UnlockBL.ps1` so encrypted drives can be unlocked before repair work begins.

### Network

Copies `Features\Network` into the mounted image when enabled. Optional Wi-Fi profile XML files can be placed under `Features\Network\WiFiProfiles`.

### Extra Files

Copies user-managed files from `Features\Extra` into the mounted image.

### Custom Scripts

Scripts in `Features\Scripts` are discovered automatically and shown in the WinPE script menu.

If you want a friendly display name, add this near the top of the script:

```powershell
# WinPE-DisplayName: My Script Name
```

### WinPE Optional Components

The GUI can query the available ADK WinPE component packages and add the ones you select during servicing.

## WinPE Menu

The staged WinPE environment can include:

- refresh IP and rerun network initialization
- map an SMB share to a drive letter
- attempt Wi-Fi workflows when WLAN support is available
- live-load staged drivers
- launch BitLocker unlock tools
- launch discovered custom scripts

## Repository Layout

```text
.
|-- Features/
|   |-- BGInfo/
|   |-- Components/
|   |-- Drivers/
|   |-- Explorer++/
|   |-- Extra/
|   |-- Menu/
|   |-- Network/
|   |-- Scripts/
|   `-- TightVNC/
|-- Boot_WIM_Mount/
|-- WinPE_Build/
|-- Start-WinPEToolkitGui.ps1
`-- README.md
```

## Important Paths

- `Features\Menu`: WinPE menu files and startup assets
- `Features\Drivers`: offline and runtime driver staging
- `Features\Network`: network files and optional Wi-Fi profiles
- `Features\Scripts`: custom PowerShell recovery scripts
- `Features\Extra`: extra files copied into the mounted image
- `Boot_WIM_Mount`: default local mount path for `boot.wim`
- `WinPE_Build`: default WinPE workspace

## Output And Metadata

During servicing, the toolkit writes `WinPEToolkit-BuildInfo.txt` into the mounted image. This records the toolkit version, build time, selected features, selected packages, driver summary, and key source paths used for the build.

The GUI also writes `WinPEToolkit.log` in the repository root while it is running.

## Included Script

`Features\Scripts\Repair-InaccessibleBootDevice.ps1` is included as a WinPE recovery script for offline boot and storage repair scenarios.

## Notes

- `Build` requires a valid WinPE `media` folder, not only a `boot.wim`
- `Commit` and `Discard` are available for mount cleanup and recovery
- passwords are intentionally excluded from settings export and import