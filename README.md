## ASUS PRIME Z590-P Hackintosh

Guide about installing macOS Monterey on ASUS PRIME Z590-P Gaming (WiFi) Mainboard with 11th Gen Intel CPU.

![PRIME Z590-P Gaming](Images/asus-z590-p.jpg)

### Information

- macOS: [Monterey 12.0.1](https://support.apple.com/en-us/HT212585)
- bootloader: [OpenCore 0.7.4](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.7.4)

---

**Table of Contents**

- [ASUS PRIME Z590-P Hackintosh](#asus-prime-z590-p-hackintosh)
  - [Information](#information)
    - [BIOS](#bios)
    - [Hardware](#hardware)
  - [Install macOS](#install-macos)
    - [1. Create OpenCore Drive](#1-create-opencore-drive)
    - [2. Create macOS Installer Drive](#2-create-macos-installer-drive)
    - [3. Install macOS](#3-install-macos)
    - [4. Post Installation](#4-post-installation)
  - [Update macOS](#update-macos)
  - [OpenCore Config](#opencore-config)
  - [Troubleshooting](#troubleshooting)
  - [Resources](#resources)
    - [ACPI Patches](#acpi-patches)
    - [Advanced Config](#advanced-config)
    - [Kexts in use](#kexts-in-use)
    - [Tools](#tools)
  - [Contribution](#contribution)
    - [Links and Documentation](#links-and-documentation)

---

#### BIOS

- Update to version 1017 (firmware in [BIOS](/BIOS) folder)
- Use following BIOS settings (DEL/F2 on boot):

  ```sh
  Ai Tweaker
    - Ai Overclock Tuner: XMP I
    - OC Tuner: OC Tuner II
  Advanced
    - System Agent (SA)-Configuration
      - Graphics Configuration
        - iGPU Multi-Monitor: Disabled
    - PCH Storage Configuration
      - SATA6G_(1-4) Hot Plug: Enabled
    - Thunderbolt(TM) Configuration
      - Discrete Thunderbolt(TM) Support: Disabled
    - PCI Subsystem Settings
      - Above 4G Decoding: Enabled
    - USB Configuration
      - Legacy USB Support: Enabled
      - XHCI Hand-off: Enabled
    - Onboard Devices Configuration
      - Serial Port Configuration
        - Serial Port: Disabled
  Boot
    - CSM (Compatibility Support Module)
      - Launch CSM: Disabled
    - Secure Boot
      - OS Type: Windows UEFI mode
      - Key Management
        - Clear Secure Boot Keys: Execute
    - Boot Configuration
      - Fast Boot: Disabled
      - Wait For 'F1' If Error: ???
  ```

#### Hardware

| Component    | Variant                | Link                                                                                                                                     |
| ------------ | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Mainboard    | ASUS PRIME Z590-P      | [www.asus.com](https://www.asus.com/Motherboards-Components/Motherboards/PRIME/PRIME-Z590-P-CSM/)                                        |
| Processor    | Intel Core i7 11700K   | [ark.intel.com](https://ark.intel.com/content/www/us/en/ark/products/212047/intel-core-i711700k-processor-16m-cache-up-to-5-00-ghz.html) |
| DDR4 RAM     | Crucial Ballistix 32GB | [www.crucial.com](https://www.crucial.com/memory/ddr4/bl2k16g32c16u4b)                                                                   |
| NVMe SSD     | Samsung 980 Pro 1TB    | [www.samsung.com](https://www.samsung.com/us/computing/memory-storage/solid-state-drives/980-pro-pcie-4-0-nvme-ssd-1tb-mz-v8p1t0b-am/)   |
| Graphics     | ROG Strix RX570 4G     | [rog.asus.com](https://rog.asus.com/graphics-cards/graphics-cards/rog-strix/rog-strix-rx570-o4g-gaming-model/)                           |
| WiFi / BT    | Fenvi FV T919 PCI-E    | [www.fenvi.com](https://www.fenvi.com/product_detail_16.html)                                                                            |
| SATA / eSata | DIGITUS DS-30104-1     | [www.digitus.info](https://www.digitus.info/de/produkte/computer-und-office-zubehoer/computer-zubehoer/io-karten/ds-30104-1/?PL=en)      |

---

### Install macOS

#### 1. Create OpenCore Drive

**a) Preparation**

- Format USB-Drive with GUID and APFS ([Link](https://www.howtogeek.com/272741/how-to-format-a-drive-with-the-apfs-file-system-on-macos-sierra/))

  - Find the correct disk number of USB-Drive:

    ```sh
    diskutil list
    ```

  - Replace {n} with corresponding disk number and {Volume} with desired Name:

    ```sh
    diskutil apfs createContainer /dev/disk{n}
    diskutil apfs addVolume disk{n} APFS {Volume}
    ```

- Download latest OpenCore: [acidanthera/opencorepkg](https://github.com/acidanthera/opencorepkg/releases)
  - Chose `debug` for installation and build or `release` for final use

**b) Install OpenCore**

- Follow this guide [OpenCore-Install-Guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)
  - Basically the files mentioned in [file-swaps](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/debug.html#file-swaps) need to be copied/updated
    - Copy `OpenCanopy.efi` to `EFI/OC/Drivers` for GUI picker
    - Copy `OpenHfsPlus.efi` to `EFI/OC/Drivers` for HFS+ support
  - Repeat this step when switching from `debug` to `release` version

**c) Add Config and Kexts**

- Copy all ACPI patches from/to `EFI/OC/ACPI/`
- Copy `config.plist` from/to `EFI/OC/config.plist`
- Copy all kexts from/to `EFI/OC/Kexts/`

---

#### 2. Create macOS Installer Drive

To create a working macOS Installer boot drive, you will need the following:

- An empty USB flash drive (minimum 16GB)
- A device already running macOS with App Store access

**a) Download macOS Installer**

- Open the Mac App Store on a device running macOS
- Download `Install macOS Monterey` application
- Close Installer when it opens up automatically

**b) Create Installer Stick**

- Follow this guide: [How to Create a Bootable macOS Monterey Installer](https://mrmacintosh.com/how-to-create-a-bootable-macos-12-beta-usb-drive-in-5-min/)
  
  or create installer stick with this command:

  ```sh
  sudo /Applications/Install\ macOS\ Monterey.app/Contents/Resources/createinstallmedia --volume /Volumes/USB
  ```

---

#### 3. Install macOS

- Connect OpenCore Drive to USB2 or USB3 port
- ⚠️ Connect macOS Installer to **USB2** port ⚠️
- Boot from OpenCore Drive (`F8` on BIOS post -> `[UEFI] OpenCore Drive`)
- Select macOS Installer (`Install macOS Monterey`)
- Begin installation on APFS formatted HDD/SSD
- On reboots select `(Install) Monterey` drive (auto)
- Finish the onboarding macOS setup process

---

#### 4. Post Installation

**a) OpenCore**

- After successful install copy OpenCore to system EFI partition
- Repeat steps 1b - 1c but with EFI on macOS HDD as target
  - Switch OpenCore from `debug` to `release` version ([file-swaps](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/debug.html#file-swaps))
  - To disable all logging apply following [config-changes](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/debug.html#config-changes)

**b) System-Tools**

- Install the following from [Tools](/Tools) folder:
  - `OpenCore Configurator` (OCC) to modify/update `config.plist`
  - `Hackintool` to check loaded kexts, system settings and more

**c) Drivers/Kexts**

- Install VoodooHDA (by [yahgoo/installVoodooHDA4BSnMont](https://github.com/yahgoo/installVoodooHDA4BSnMont))
  1. In OpenCore Configurator go to `NVRAM` -> `csr-active-config` and set

      | Key*              | Value      | Type |
      | ----------------- | ---------- | ---- |
      | csr-active-config | `EF0F0000` | DATA |

  2. Boot into recovery mode, open up terminal, disable SIP with `csrutil` and `authenticated-root`, reboot afterwards

      ```sh
      csrutil disable
      csrutil authenticated-root disable
      reboot
      ```

      Check `SIP` status after reboot (should both be `disabled`)

      ```sh
      csrutil status
      csrutil authenticated-root status
      ```

  3. Check volume label for `Montery` drive (might be `disk1s5`)

      ```sh
      diskutil list
      ```

      Mount System-Snapshot of `Montery` volume by label

      ```sh
      sudo diskutil mountdisk disk1s5
      ```

  4. Disable `GateKeeper` and verify status (should be `disabled`)

      ```sh
      sudo spctl --master-disable
      spctl --status
      ```

  5. Copy `VoodooHDA.kext` to `L/E`

      ```sh
      sudo cp -R /VoodooHDA.kext /Library/Extensions
      ```

  6. Wait for prompt `System Extension Updated` and accept it. Open System Preferences and allow kext modification, **don't reboot**.
  7. Open `Hackintool` -> `Utilities` -> `Install Kext(s)` and select `VoodooHDA.kext`.
  8. Reboot and check if audio device is working as expected.
  9. Revert all changes (but `csr-active-config`):

        - Re-Enable `GateKeeper` and verify status (should be `enabled`)

          ```sh
          sudo spctl --master-enable
          spctl --status
          ```

        - Re-Enable `SIP` after booting in recovery mode

          ```sh
          csrutil authenticated-root enable
          csrutil enable
          reboot
          ```

          Check `SIP` status after reboot (should both be `enabled`)

          ```sh
          csrutil status
          csrutil authenticated-root status
          ```

---

### Update macOS

- Make a full backup with `Time Machine` or similar software
- Check the official update-guide: [OpenCore-Post-Install/update](https://dortania.github.io/OpenCore-Post-Install/universal/update.html)
- Download latest version of OpenCore
- Download updates for all installed kexts
- Update OpenCore Drive for testing purpose
  - Use latest OpenCore, kexts and drivers
- Boot from OpenCore Drive
- If the system boots
  - Mount EFI partition of macOS HDD
  - Replace EFI from OpenCore Drive
- If the system boots
  - Start macOS Update from `System Settings` -> `Software Update`
  - With OpenCore the update process should work automatically
    - If `Software Update` shows `Mac version is up to date`, download macOS Installer from AppStore and initialize the update manually
- If system doesn't boot on one of these steps
  - Try to fix the problem or revert to the latest backup

---

### OpenCore Config

For adding your SSDTs, Kexts and Firmware Drivers to create snapshots of your populated EFI folder ([link](https://dortania.github.io/OpenCore-Install-Guide/config.plist/#adding-your-ssdts-kexts-and-firmware-drivers)) use [corpnewt/ProperTree](https://github.com/corpnewt/ProperTree)

**Add ACPI patches**

To manually add ACPI patches do the following

- Copy `{name}.aml` into `EFI/OC/ACPI`
- Open `config.plist` in OpenCore Configurator
- Add new files with `ACPI` -> `Scan/Browse`
  - Select `EFI/OC/ACPI` folder
  - Add a meaningful `Comment` to SSDT

**Add kexts**

To manually add kexts do the following

- Copy `{name}.kext` into `EFI/OC/Kexts`
- Open `config.plist` in OpenCore Configurator
- Add new files with `Kernel` -> `Scan/Browse`
  - Select `EFI/OC/Kexts` folder
  - Add a meaningful `Comment` to kext
  - (Optional: set `MinKernel` and `MaxKernel`)

---

### Troubleshooting

Tips and tricks to solve already known problems

**Reset NVRAM**

NVRAM can be reset from OpenCanopy boot picker if auxiliary-entries are displayed in OpenCore ([Link](https://www.reddit.com/r/hackintosh/comments/h0jkjl/hide_partitions_from_opencore_boot_screen/))

- Open `config.plist` with OpenCore Configurator
- Go to `Misc` -> `Boot` and set `HideAuxiliary = NO`
- On reboot select `Reset NVRAM` from tools

**AHCI Ports**

Information copied from [SATA Drives Not Shown in DiskUtility](https://www.olarila.com/topic/9616-error-while-installing-big-sur/?do=findComment&comment=117695)

- Make sure SATA Mode is AHCI in bios
- Certain SATA controllers may not be officially supported by macOS, for these cases you'll want to grab [CtlnaAHCIPort.kext](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/CtlnaAHCIPort.kext.zip)
  - For very legacy SATA controllers, [AHCIPortInjector.kext](https://www.insanelymac.com/forum/files/file/436-ahciportinjectorkext/) may be more suitable

**Apple Watch Unlock**

If unlock with Apple Watch doesn't work or make problems although using a `BCM94360CD Fenvi` card, follow the steps of this blogpost comment: [watchOS 7 Beta 5 - unlock mac doesn't work](https://forums.macrumors.com/threads/watchos-7-beta-5-unlock-mac-doesnt-work.2250819/page-2?post=28904426#post-28904426). Afterwards unlock with Apple Watch works like it should with a regular Mac.

**Intel Power Gadget**

The latest version of `Intel Power Gadget` (v3.7.0) is causing  kernel panics when waking from sleep when using SMBIOS `iMacPro1,1`. It doesn't happen with `iMac20,2` SMBIOS, but the overall system speed is lower then. So monitoring of CPU frequency and speed stepping is not possible unless a new version of `Intel Power Gadget` will be released.

**AppleALC**

The `Audio Codec` of ASUS PRIME Z590-P is [Realtek ALC897](https://www.asus.com/de/Motherboards-Components/Motherboards/PRIME/PRIME-Z590-P/techspec/). Although it's listed on `AppleALC` [Supported Codecs](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs), none of the possible layout-ids (12, 23, 66, 69) worked. The device is recognized (with different outputs for each layout), but no audio output was possible.

---

### Resources

Useful information, tips and tutorials used to create this Hackintosh

#### ACPI Patches

Several SSDT patches are [recommended](https://dortania.github.io/Getting-Started-With-ACPI/ssdt-methods/ssdt-prebuilt.html#desktop-comet-lake) to fix following problems

| Problem                     | Patch            | Link                                                                                                                     |
| --------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Fixing System Clocks        | SSDT-AWAC.aml    | [dortania/acpi/awac-methods](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac-methods/prebuilt.html)  |
| Fixing Embedded Controllers | SSDT-EC-USBX.aml | [dortania/acpi/ec-fix]([https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)                      |
| Fixing Power Management     | SSDT-PLUG.aml    | [dortania/acpi/plug]([https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)                          |
| Fixing RHUB: SSDTTime       | SSDT-RHUB.aml    | [dortania/acpi/rhub-methods]([https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub-methods/ssdttime.html) |

---

#### Advanced Config

The following configurations are not essential for the Hackintosh to work, but they improve functionality to get as close to a real Mac as possible.

**CPU Config**

In OpenCore Configurator go to `Kernel` -> `Add` and set

| Emulate    |                                       |
| ---------- | ------------------------------------- |
| Cpuid1Data | `EB060900 00000000 00000000 00000000` |
| Cpuid1Mask | `FFFFFFFF 00000000 00000000 00000000` |

**Apple Secure Boot**

In OpenCore Configurator go to `Misc` -> `Security` and set

| Security        |                                   |
| --------------- | --------------------------------- |
| DmgLoading      | `Signed`                          |
| SecureBootModel | `j137 iMacPro1,1 (December 2017)` |

**USB Mapping**

An USB port-mapping was created with [OpenCore Post-Install - USB Mapping](https://dortania.github.io/OpenCore-Post-Install/usb/intel-mapping/intel.html) guide and [corpnewt/USBMap](https://github.com/corpnewt/USBMap) tool. The following exported files can be found in [USB](/USB) folder:

| Path         | File                | Descrition             |
| ------------ | ------------------- | ---------------------- |
| USB/         | SSDT-RHUB-Reset.aml | compiled ssdt-patch    |
| USB/Results/ | SSDT-RHUB-Reset.dsl | ssdt-patch source file |
| USB/Results/ | USBMap.kext         | USB port-mapping kext  |

**RTC / F1 Error**

If `The system has POSTed in safe mode` error appears after each reboot:

In OpenCore Configurator go to `Kernel` -> `Patch` and add the following [patch](https://github.com/jergoo/Hackintosh-ROG-STRIX-Z490I#f1-boot-error):

| Identifier                | Comment        | Find     | Replace  | Count | Enabled |
| ------------------------- | -------------- | -------- | -------- | ----- | ------- |
| com.apple.driver.AppleRTC | F1 error patch | 75330FB7 | EB330FB7 | 1     | &#9745; |

If the error still appears on cold-boots (after power-off), go to `Kernel` -> `Quirks` in OpenCore Configurator and enable &#9745; `DisableRtcChecksum` Quirk ([Fixing RTC write issues](https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html#finding-our-bad-rtc-region)).

**Bluetooth / Wake**

The internal USB2-ports share the same hub and are used for I/O-Panel and Card-Reader connectors. Therefore an [USB3 to USB2 internal Adapter](https://www.amazon.com/SIENOC-Female-Motherboard-Housing-Adapter/dp/B00EOI3VC8) is used for the Bluetooth-USB connector. To fix connection loss after sleep perform the [Fixing Sleep Preparations](https://dortania.github.io/OpenCore-Post-Install/universal/sleep.html#preparations):

```sh
sudo pmset autopoweroff 0
sudo pmset powernap 0
sudo pmset standby 0
sudo pmset proximitywake 0
sudo pmset tcpkeepalive 0
```

---

#### Kexts in use

| Type         | Kext                                                         | Version | Author                                                                                                            |
| ------------ | ------------------------------------------------------------ | ------- | ----------------------------------------------------------------------------------------------------------------- |
| Patch Engine | Lilu.kext                                                    | 1.5.6   | [acidanthera/Lilu](https://github.com/acidanthera/Lilu)                                                           |
| Graphics     | WhateverGreen.kext                                           | 1.5.4   | [acidanthera/WhateverGreen](https://github.com/acidanthera/WhateverGreen)                                         |
| Sensors      | VirtualSMC.kext <br> SMCSuperIO.kext <br>  SMCProcessor.kext | 1.2.7   | [acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC)                                               |
| Audio        | VodooHDA.kext                                                | 2.9.7   | [sourceforge.net](https://sourceforge.net/projects/voodoohda/)                                                    |
| Ethernet     | IntelMausi.kext                                              | 1.0.7   | [Mieze/LucyRTL8125Ethernet](https://github.com/Mieze/LucyRTL8125Ethernet)                                         |
| NVMe         | NVMeFix.kext                                                 | 1.0.9   | [acidanthera/NVMeFix](https://github.com/acidanthera/NVMeFix)                                                     |
| CPU Temp     | XHCI-unsupported.kext                                        | 0.9.2   | [RehabMan/OS-X-USB-Inject-All](https://github.com/RehabMan/OS-X-USB-Inject-All/tree/master/XHCI-unsupported.kext) |
| (USB Map     | USBInjectAll.kext                                            | 0.7.6   | [Sniki/OS-X-USB-Inject-All](https://github.com/Sniki/OS-X-USB-Inject-All))                                        |

---

#### Tools

| Name                    | Version  | Download                                                                                                    |
| ----------------------- | -------- | ----------------------------------------------------------------------------------------------------------- |
| OpenCore Configurator   | 2.52.0.0 | [mackie100projects](https://mackie100projects.altervista.org/download-opencore-configurator/)               |
| Hackintool              | 3.6.2    | [headkaze/Hackintool](https://github.com/headkaze/Hackintool/)                                              |
| 🚨 Intel Power Gadget* 🚨 | 3.7.0    | [software.intel.com](https://software.intel.com/content/www/us/en/develop/articles/intel-power-gadget.html) |
| IORegistryExplorer      | 2.1      | [vulgo/IORegistryExplorer](https://github.com/vulgo/IORegistryExplorer)                                     |
| MaciASL                 | 1.6.2    | [acidanthera/MaciASL](https://github.com/acidanthera/MaciASL/)                                              |
| USBMap                  | -        | [corpnewt/USBMap](https://github.com/corpnewt/USBMap)                                                       |

*\*Causes Kernel Panic after Sleep on iMacPro1,1*

---

### Contribution

This Hackintosh was build with help of the following repositories and guides:

| Help on Issue                    | Source                                                                                                                        |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Motivation and Hardware          | [SchmockLord/Gigabyte-Z590i-Vision-D-11900k](https://github.com/SchmockLord/Gigabyte-Z590i-Vision-D-11900k)                   |
| BIOS and OpenCore Config         | [yilmazca/intel-i9-10900K-Asus-prime-Z490A](https://github.com/yilmazca/intel-i9-10900K-Asus-prime-Z490A-hackintosh)          |
| F1 Boot Error and BIOS           | [jergoo/Hackintosh-ROG-STRIX-Z490I](https://github.com/jergoo/Hackintosh-ROG-STRIX-Z490I#f1-boot-error)                       |
| OpenCore Config and Installation | [OpenCore Install Guide - Desktop Comet Lake](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html) |
| Installing VoodooHDA             | [yahgoo/installVoodooHDA4BSnMont](https://github.com/yahgoo/installVoodooHDA4BSnMont)                                         |

#### Links and Documentation

**Motivation**

- [Olarila HackBeast GA Z590 UC AD / Core i9 11900k](https://www.olarila.com/topic/13650-olarila-hackbeast-ga-z590-uc-ad-core-i9-11900k-with-thunderbolt-full-dsdt-patches-guide-and-discussion/)
- [Lorys89/ASROCK_Z590M-ITX-AX_-_i5-11600K](https://github.com/Lorys89/ASROCK_Z590M-ITX-AX_-_i5-11600K_-_RX5600XT)
- [Big Sur on Z590, 11700K, Maximus XIII Hero](https://www.reddit.com/r/hackintosh/comments/moq26b/big_sur_on_z590_11700k_maximus_xiii_hero_opencore/)
- [i7 10700k + ROG STRIX Z490-A GAMING + RX 590](https://www.reddit.com/r/hackintosh/comments/gvdrns/i7_10700k_rog_strix_z490a_gaming_rx_590/)
- [Asus ROG Strix Z490-E Gaming + i9 10900K + OpenCore](https://www.tonymacx86.com/threads/success-asus-rog-strix-z490-e-gaming-i9-10900k-opencore.299137/)

**Hardware**

- [About the Z490 Z590 motherboard](https://www.reddit.com/r/hackintosh/comments/m7ieb3/about_the_z490_z590_motherboard/)
- [Anyone tested 11th gen i5/i7/i9?](https://www.reddit.com/r/hackintosh/comments/mj7yhd/anyone_tested_11th_gen_i5i7i9/)

**Boot Flags**

- [GPU Buyers Guide - Boot Flags](https://dortania.github.io/GPU-Buyers-Guide/misc/bootflag.html)
- [LiLu & Plugins mit Bootflags und Beispielen](https://www.hackintosh-forum.de/forum/thread/32411-lilu-plugins-mit-bootflags-und-beispielen/)

**Installation**

- [OpenCore Install Guide - macOS 12: Monterey](https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html)
- [Stuck in [apfs_module_start 1689 load com.apple.filesystems.apfs]](https://www.reddit.com/r/hackintosh/comments/iidh7l/stuck_in_apfs_module_start_1689_load/)
- [AppleUSBXHCIPort::resetandcreatedevice: failed to create device](https://www.tonymacx86.com/threads/solved-appleusbxhciport-resetandcreatedevice-failed-to-create-device.230074/)

**Post-Install**

- [ASUS PRIME Z590-P OpenCore - Probleme nach der Installation](https://www.hackintosh-forum.de/forum/thread/54911-asus-prime-z590-p-opencore-probleme-nach-der-installation/)
- [acidanthera/OpenCorePkg - AppleKeyboardLayouts](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/AppleKeyboardLayouts/AppleKeyboardLayouts.txt)
- [How can I disable HDMI audio from my Radeon 580?](https://www.reddit.com/r/hackintosh/comments/gqa09d/how_can_i_disable_hdmi_audio_from_my_radeon_580/)
- [voodooHDA 2.9.7 intermittently works on Big Sur 11.5 beta](https://www.insanelymac.com/forum/topic/348181-voodoohda-297-intermittently-works-on-big-sur-115-beta/)
- [Updated Tips and Observations for Big Sur and Monterey](https://www.insanelymac.com/forum/topic/346639-updated-tips-and-observations-for-big-sur-and-monterey/)

**USB-Mapping**

- [EliteMacx86 - How to Fix USB Ports on macOS](https://elitemacx86.com/threads/how-to-fix-usb-ports-on-macos.691/)
- [Activate internal USB Header for Fenvi T919 - ASUS PRIME Z390-A](https://hackintosher.com/forums/thread/activate-internal-usb-header-for-fenvi-t919-asus-prime-z390-a-hackintosh-build-guide-w-rx-5700-xt.10083/)
- [Problem beim USB Mapping / USB Header für Fenvi Karte nicht erkannt](https://www.hackintosh-forum.de/forum/thread/50161-problem-beim-usb-mapping-usb-header-f%C3%BCr-fenvi-karte-nicht-erkannt/)
- [Z490, Internal 2.0 headers not showing up](https://www.reddit.com/r/hackintosh/comments/gylqgi/z490_internal_20_headers_not_showing_up/)
- [Intel-i9-10900-Gigabyte-Z490-Vision-G - USB Port Configuration](https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh/blob/master/USB-Port-Configuration.md)
- [SSDT-RHUB not working with Usb 2.0](https://www.reddit.com/r/hackintosh/comments/ni8w5n/ssdtrhub_not_working_with_usb_20/)

**AppleALC**

- [OpenCore Post-Install - Fixing audio with AppleALC](https://dortania.github.io/OpenCore-Post-Install/universal/audio.html#finding-your-layout-id)
- [OpenCore Post-Install - AppleALC working inconsistently](https://dortania.github.io/OpenCore-Post-Install/universal/audio.html#applealc-working-inconsistently)
- [AppleALC: Stopped working on macOS Monterey](https://github.com/acidanthera/bugtracker/issues/1707)
- [No Audio with OpenCore on Big Sur, but Works with Clover on Catalina](https://www.tonymacx86.com/threads/solved-no-audio-with-opencore-on-big-sur-but-works-with-clover-on-catalina-lenovo-ideapad-p500.307113/page-2)

**DRM**

- [OpenCore Post-Install - Fixing DRM support and iGPU performance](https://dortania.github.io/OpenCore-Post-Install/universal/drm.html)
- [acidanthera/WhateverGreen - DRM Compatibility Chart for 10.15](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md)

**Intel Power Gadget**

- [System crash with Intel Power Gadget & iStats Menu](https://www.reddit.com/r/hackintosh/comments/hoe2am/system_crash_with_intel_power_gadget_istats_menu/)
- [Intel Power Gadget (Mac) causes kernel panic](https://community.intel.com/t5/Processors/Intel-Power-Gadget-Mac-causes-kernel-panic/td-p/677739)
- [Missing IGPU trace with Intel Power Gadget](https://www.tonymacx86.com/threads/solved-missing-igpu-trace-with-intel-power-gadget.280757/)

**Security**

- [Dortania Bugtracker - csr-active-config='E7030000'?](https://github.com/dortania/bugtracker/issues/32)
- [OpenCore Post-Install - Apple Secure Boot](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html#apple-secure-boot)
- [Modification of System Files Using "csrutil authenticated-root disable"](https://forums.macrumors.com/threads/words-of-caution-regarding-modification-of-system-files-using-csrutil-authenticated-root-disable.2276764/)
- [How to Disable Gatekeeper from Command Line in Mac OS X](https://osxdaily.com/2015/05/04/disable-gatekeeper-command-line-mac-osx/)

**Performance**

- [XMP 1 and XMP 2 difference](https://rog.asus.com/forum/showthread.php?106270-XMP-1-and-XMP-2-difference)
- [Downside of using smbios Imac Pro 1,1 for running non-Xeon CPU](https://www.reddit.com/r/hackintosh/comments/fmbl0q/downside_of_using_smbios_imac_pro_11_for_running/)
- [cpu-monkey - Intel Core i7-11700K vs. Apple M1](https://www.cpu-monkey.com/de/compare_cpu-intel_core_i7_11700k-vs-apple_m1)
- [cpu-monkey - Intel Core i7-11700K vs. Intel Core i9-11900K](https://www.cpu-monkey.com/de/compare_cpu-intel_core_i7_11700k-vs-intel_core_i9_11900k)
