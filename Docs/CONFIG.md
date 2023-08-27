# Advanced Config Z590-P

The following configurations are not essential for the Hackintosh to work, but they improve functionality to get as close to a real Mac as possible.

## CPU Config

In OpenCore Configurator go to `Kernel` -> `Add` and set

| Emulate    |                                       |
| ---------- | ------------------------------------- |
| Cpuid1Data | `EB060900 00000000 00000000 00000000` |
| Cpuid1Mask | `FFFFFFFF 00000000 00000000 00000000` |

## Apple Secure Boot

In OpenCore Configurator go to `Misc` -> `Security` and set

| Security        |                                   |
| --------------- | --------------------------------- |
| DmgLoading      | `Signed`                          |
| SecureBootModel | `j137 iMacPro1,1 (December 2017)` |

*Note: this will noticeable slow down initial startup time after login*

## USB Mapping

An USB port-mapping can be created with [OpenCore Post-Install - USB Mapping](https://dortania.github.io/OpenCore-Post-Install/usb/intel-mapping/intel.html) guide and [corpnewt/USBMap](https://github.com/corpnewt/USBMap) tool.

- Use Back-Panel USB2-Ports for Mouse and Keyboard
- Use `SSDT-USB-Reset.aml` (or `SSDT-RHUB.aml`) for USB-Reset
- Boot with `USBInjectAll.kext` and &#9745; `XhciPortLimit` enabled
- Run `USBmap.command` and test all ports with USB2/3/C Devices
- Give each port a unique name like e.g. `USB3.0(HS) Back Left`
- After discovery and naming set the correct port-types
  - USB2: 0
  - USB3: 3
  - USB-C: 9
  - Internal: 255
- Export `USBMap.kext` and add it to EFI and OpenCore
- Disable `XhciPortLimit` and remove `USBInjectAll.kext`

The following generated files can be found in [USB](../USB) folder:

| Path         | File        | Description           |
| ------------ | ----------- | --------------------- |
| USB/Results/ | USBMap.kext | USB port-mapping kext |
| USB/Scripts/ | USB.plist   | USB port-naming list  |

## RTC / F1 Error

If `The system has POSTed in safe mode` error appears after each reboot:

In OpenCore Configurator go to `Kernel` -> `Patch` and add the following [patch](https://github.com/jergoo/Hackintosh-ROG-STRIX-Z490I#f1-boot-error):

| Identifier                | Comment        | Find     | Replace  | Count | Enabled |
| ------------------------- | -------------- | -------- | -------- | ----- | ------- |
| com.apple.driver.AppleRTC | F1 Error Patch | 75330FB7 | EB330FB7 | 1     | &#9745; |

If the error still appears on cold-boots (after power-off), add `RTCMemoryFixup` to your kexts and enable `DisableRtcChecksum` Quirk or try to [find your bad rtc region](https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html#finding-our-bad-rtc-region).

## Sleep/Wake

It can happen that the system is waking up unwanted from sleep (e.g. darkwake with display(s) staying off). Check sleep/wake counts with:

```sh
pmset -g stats
```

For a list of know issues and possible fixes see [Docs/SLEEP.md](./SLEEP.md).

## Card Reader

The generic Card-Reader doesn't show up in system preferences by default. Use [Generic Card Reader Driver Friend](https://github.com/0xFireWolf/GenericCardReaderFriend) to display some information. Follow this [guide](https://github.com/0xFireWolf/GenericCardReaderFriend/blob/main/FAQ.md) to find the correct `Product ID` and `Vendor ID` that needs to be set in the kexts `info.plist`.

## SATA Hot-Plug

To get SATA Hot-Plug working, make sure the feature is enabled in BIOS, and apply the following patch in OCC -> Kernel -> Patch:

| Identifier                     | Comment       | Find     | Replace  | Count | Enabled |
| ------------------------------ | ------------- | -------- | -------- | ----- | ------- |
| com.apple.driver.AppleAHCIPort | SATA Hot Plug | 40600200 | 00000000 | 1     | &#9745; |

If drives are showing up as external, in OpenCore Configurator go to `Kernel` -> `Quirks` and check the `ExternalDiskIcons` Quirk.

## Audio

The `Audio Codec` of ASUS PRIME Z590-P is [Realtek ALC897](https://www.asus.com/de/Motherboards-Components/Motherboards/PRIME/PRIME-Z590-P/techspec/). Layout-id 11 on `AppleALC` [Supported Codecs](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs) gives basic audio output.

- For AppleHDA add `AppleHDA.kext` and `alcid=11` bootflag

VoodooHDA is also working (requires harder setup) and can be used for advanced audio configuration.

- For VoodooHDA disable `AppleHDA.kext`, remove `alcid=11` bootflag and follow [Docs/AUDIO.md](./AUDIO.md)

*Note: Every macOS updates requires VoodooHDA to be re-installed again!*
