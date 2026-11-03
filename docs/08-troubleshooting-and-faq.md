# 08 — Troubleshooting & FAQ

## ADB is not recognised
Open PowerShell inside the extracted Platform-Tools folder and run:
```powershell
.\adb.exe version
```
Alternatively, add Platform-Tools to PATH.

## Device says unauthorized
Unlock the phone and approve the RSA fingerprint prompt only for a trusted PC.

## Device is not listed
Check the data cable, USB port, connection mode, Windows driver, USB debugging and whether the phone is unlocked.
```powershell
.\adb.exe devices
```

## SecurityException: cannot disable system packages
Firmware is restricting the operation. Do not bypass protection. Look for a supported settings control or leave the package unchanged.

## Unknown package or no output
Check spelling and search package records:
```powershell
.\adb.exe shell pm list packages -u | findstr /i "partial-name"
```

## Why does output differ from a tutorial?
MIUI/HyperOS, Android releases, regions and device variants differ. A tutorial for another model may not apply.

## Does removing `com.miui.analytics` stop all Xiaomi telemetry?
No. Removing one package does not establish that all telemetry or diagnostics are disabled.

## Does Internet permission prove data collection?
No. It does not prove a connection occurred or identify transmitted content.

## Does TRIM make deleted files unrecoverable?
Not necessarily. TRIM/discard and garbage collection do not prove physical erasure of every NAND page.

## Does `pm clear` clear only cache?
No. It clears app data. Use the device UI's Clear cache option when that is the intended action.

## Can these commands be used on every Xiaomi phone?
Do not assume so. Model, region, Android version and firmware build can change package availability and dependencies.

## Finish an ADB session safely
Disconnect the phone, turn USB debugging off and revoke USB debugging authorisations if available.
