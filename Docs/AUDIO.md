# Install VoodooHDA.kext in macOS Monterey

Guide inspired by [yahgoo/installVoodooHDA4BSnMont](https://github.com/yahgoo/installVoodooHDA4BSnMont)

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
