# 02 — ADB Setup & Device Preparation

## What is ADB?
Android Debug Bridge is a developer tool for communicating with Android devices. It can inspect packages, run shell commands, collect selected diagnostics and manage apps for an Android user. It is Android-wide, while this repository documents Xiaomi-specific use.

## Requirements
- Windows 10/11
- Official Android SDK Platform-Tools
- USB data cable
- Xiaomi/Redmi/POCO device you own or are authorised to administer
- Backup of important data before modifications

Download: https://developer.android.com/tools/releases/platform-tools

## Prepare the phone
Menu names vary by MIUI/HyperOS release.
1. Open Settings → About phone.
2. Tap the OS/MIUI/HyperOS version entry repeatedly to enable Developer options.
3. Open Settings → Additional settings → Developer options (path may differ).
4. Enable USB debugging only while needed.
5. Connect the phone, unlock it and approve the RSA prompt only for a trusted PC.

Do not enable OEM unlocking or unlock the bootloader for this project.

## Prepare Windows
Extract Platform-Tools. Open the extracted folder in File Explorer, enter `powershell` in the address bar and press Enter.

```powershell
.\adb.exe version
.\adb.exe devices
```

Expected state is `device`. `unauthorized` means approve the prompt on the phone. If no device appears, check the cable, USB port, driver and debugging setting.

## Device identification
Run individually:
```powershell
.\adb.exe shell getprop ro.product.manufacturer
.\adb.exe shell getprop ro.product.model
.\adb.exe shell getprop ro.build.version.release
.\adb.exe shell getprop ro.build.fingerprint
```

Some properties may be blank or differ by firmware.

## Finish safely
Disconnect the cable, turn USB debugging off and use Revoke USB debugging authorizations if available. Never leave an untrusted PC authorised.
