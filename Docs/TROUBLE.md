# Troubleshooting Z590-P

Tips and tricks to solve already known problems

When [adding SSDTs, Kexts and Firmware Drivers](https://dortania.github.io/OpenCore-Install-Guide/config.plist/#adding-your-ssdts-kexts-and-firmware-drivers) to the EFI folder, use [OpenCore Configurator](https://mackie100projects.altervista.org/opencore-configurator/) as GUI or [corpnewt/ProperTree](https://github.com/corpnewt/ProperTree) when needing an universal plist-editor.

## Add kexts

To manually add kexts:

- Copy `{name}.kext` into `EFI/OC/Kexts`
- Open `config.plist` in OpenCore Configurator
- Add new files with `Kernel` -> `Scan/Browse`
  - Select `EFI/OC/Kexts` folder
  - Add a meaningful `Comment` to kext
  - (Optional: set `MinKernel` and `MaxKernel`)

## Add ACPI patches

To manually add ACPI patches:

- Copy `{name}.aml` into `EFI/OC/ACPI`
- Open `config.plist` in OpenCore Configurator
- Add new files with `ACPI` -> `Scan/Browse`
  - Select `EFI/OC/ACPI` folder
  - Add a meaningful `Comment` to SSDT

## Reset NVRAM

In OpenCore Configurator go to `Misc` -> `Boot` and uncheck the option `HideAuxiliary`.

- On reboot select `Reset NVRAM` from OpenCanopy boot-options

## Default Boot Option

In OpenCore Configurator go to `Misc` -> `Security` and check the option `AllowSetDefault`.

- In OpenCanopy boot picker set default boot-option with `ctrl + enter`

## Apple Watch Unlock

If unlock with Apple Watch doesn't work or make problems although using a `BCM94360CD Fenvi` card, follow the steps of this blogpost comment: [watchOS 7 Beta 5 - unlock mac doesn't work](https://forums.macrumors.com/threads/watchos-7-beta-5-unlock-mac-doesnt-work.2250819/page-2?post=28904426#post-28904426). Afterwards unlock with Apple Watch works like a normal Mac.

## Intel Power Gadget

The latest version of `Intel Power Gadget` (v3.7.0) is causing  kernel panics when waking from sleep when using SMBIOS `iMacPro1,1`. It doesn't happen with `iMac20,2` SMBIOS, but the overall system speed is lower then. So monitoring of CPU frequency and speed stepping is not possible unless a new version of `Intel Power Gadget` will be released.
