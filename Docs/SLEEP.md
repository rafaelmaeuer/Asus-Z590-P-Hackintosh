# Sleep with Z590-P

List of known wake/sleep related issues and possible solutions or fixes.

## Power-Nap

To disable Power Nap, uncheck `Activate Power Nap` in System Settings -> Energy Saving and disable WakeUp for Maintenance with:

```sh
sudo pmset -a ttyskeepawake 0
```

## Wake by RTC

If encountering random darkwakes after some hours of sleep, check for RTC wake reasons with:

```sh
log show | grep 'Wake reason'
  > 'Wake reason: RTC (Alarm)'
#or
pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"
  > 'DarkWake from Normal Sleep [CDN] : due to RTC/Maintenance'
```

If there are entries with `RTC (Alarm)` or `RTC/Maintenance`, disable RTC wake scheduling with the following patch in OCC -> Kernel -> Patch:

| Identifier                | Comment                     | Find | Replace | Count | Enabled |
| ------------------------- | --------------------------- | ---- | ------- | ----- | ------- |
| com.apple.driver.AppleRTC | Disable RTC wake scheduling |      | C3      | 1     | &#9745; |

*Note: Another fix might be to add the `darkwake=3` bootflag.*

## Wake by Bluetooth

The internal USB2-ports share the same hub and are used for I/O-Panel and Card-Reader connectors. Therefore an [USB3 to USB2 internal Adapter](https://www.amazon.com/SIENOC-Female-Motherboard-Housing-Adapter/dp/B00EOI3VC8) is used for the Bluetooth-USB connector. [Fixing Sleep Preparations](https://dortania.github.io/OpenCore-Post-Install/universal/sleep.html#preparations) help to fix some connection loss after sleep:

```sh
sudo pmset autopoweroff 0
sudo pmset powernap 0
sudo pmset standby 0
sudo pmset proximitywake 0
sudo pmset tcpkeepalive 0
```

**Fix after Wake**

It seems that with Monterey there are [issues](https://github.com/acidanthera/bugtracker/issues/1821) with Bluetooth. You can try to enable the `ExtendBTFeatureFlags` Quirk or reset Bluetooth after wake using [SleepWatcher](https://gist.github.com/ralph089/a65840c4f5e439b90170d735a89a863f), [BetterTouchTool](https://www.tonymacx86.com/threads/bluetooth-doesnt-work-after-wake-on-monterey.315679/page-5#post-2308355) or [Shortery](https://www.tonymacx86.com/threads/bluetooth-doesnt-work-after-wake-on-monterey.315679/page-6#post-2308688).

As Disable/Enable BT with `BetterTouchTool` and `SleepWatcher` only work in one of both cases, the following combination is used to fix BT after sleep:

- Disable BT before sleep with [BetterTouchTool](https://www.tonymacx86.com/threads/bluetooth-doesnt-work-after-wake-on-monterey.315679/page-5#post-2308355)
- Enable BT after wake with [SleepWatcher](https://gist.github.com/rafaelmaeuer/57dcdbdde509942bc04b2f84783a23af) script

## Wake by Keyboard

To fix an odd bug with Intel's 100 series chipsets and newer is that sometimes macOS requires a second keyboard press or some other wake event to power up the monitor as well. This is solved with [Create a fake ACPI Device](https://dortania.github.io/OpenCore-Post-Install/usb/misc/keyboard.html#method-2-create-a-fake-acpi-device) which requires to add:

- USBWakeFixup.kext
- SSDT-USBW.aml
