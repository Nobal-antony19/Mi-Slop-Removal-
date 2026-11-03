# Xiaomi / Redmi / POCO — Safe ADB Debloating Workflow

> **Purpose:** A practical, repeatable workflow for inspecting Xiaomi system apps, deciding what is safe to remove for the current Android user, applying changes, verifying them and restoring packages when necessary.
>
> **Scope:** Xiaomi, Redmi and POCO devices running MIUI or HyperOS. Package names and behaviour vary by model, region, Android version and firmware build. This is a research workflow, not a universal debloat list.

---

## 1. Ground rules

1. **Identify before removing.** A package name alone is not enough to establish what an app does.
2. **Inspect before changing.** Capture package path, version, installer, requested permissions and components where available.
3. **Prefer reversible user-level removal.** Use `pm uninstall --user 0`, not root, partition edits or APK deletion.
4. **Change one package at a time.** Verify the result and test normal device functions before moving on.
5. **Keep a change log.** Record the device model, region, HyperOS/MIUI build, exact command, output and test result.
6. **Do not disable core services.** Avoid thermal, power, package installer, permission controller, System UI, launcher, telephony, networking, security, update and input-method components unless there is a device-specific, well-tested reason.
7. **Do not assume telemetry is gone.** Removing one analytics package or revoking one authorization does not prove all data collection has stopped.

### What the ADB actions mean

| Action | Meaning | Practical note |
|---|---|---|
| `pm list packages` | Lists packages known to Package Manager | Read-only |
| `dumpsys package PACKAGE` | Shows package metadata and components | Read-only |
| `pm disable-user --user 0 PACKAGE` | Tries to disable a package for user 0 | May be blocked by HyperOS |
| `pm uninstall --user 0 PACKAGE` | Uninstalls the package for user 0 | Usually reversible if the system image still contains it |
| `cmd package install-existing PACKAGE` | Attempts to restore a preloaded package for the current user | Works only if the package remains available to the system |
| `pm path PACKAGE` | Shows the installed APK path, if available | Read-only |

**Important:** `pm uninstall --user 0` is not the same as deleting the APK from `/system`, `/product` or `/vendor`. It changes the package's installed state for the specified Android user.

---

## 2. Prepare the device and computer

### On the phone

1. Open **Settings → About phone**.
2. Record the exact device model and HyperOS/MIUI version/build.
3. Enable Developer options by tapping the OS version entry repeatedly, if required by that build.
4. Open **Settings → Additional settings → Developer options**.
5. Enable **USB debugging**.
6. Connect the phone to the computer and approve the RSA debugging prompt only on your own trusted computer.

Menu names vary between builds.

### On Windows PowerShell

Open PowerShell in the folder containing Android Platform Tools. In this investigation, the folder was:

```powershell
cd "C:\Users\nobel\Downloads\platform-tools-latest-windows\platform-tools"
```

Check ADB:

```powershell
.\adb.exe version
.\adb.exe devices
```

The device should appear as `device`, not `unauthorized` or `offline`.

If more than one device is connected, use the appropriate ADB serial with `-s SERIAL` in commands. Do not copy a serial number into a public repository.

---

## 3. Create a baseline before changing anything

Save a package inventory:

```powershell
.\adb.exe shell pm list packages > packages-before.txt
```

Save third-party packages separately:

```powershell
.\adb.exe shell pm list packages -3 > third-party-before.txt
```

Save package paths for a package under investigation:

```powershell
.\adb.exe shell pm path PACKAGE_NAME
```

Inspect its metadata:

```powershell
.\adb.exe shell dumpsys package PACKAGE_NAME
```

For a shorter search in PowerShell:

```powershell
.\adb.exe shell dumpsys package PACKAGE_NAME | Select-String -Pattern "versionName|versionCode|codePath|userId|requested permissions|install permissions|User 0|enabled="
```

The output format varies by Android version. Review the complete dump when the filtered output is insufficient.

### What to look for

- Package name and version.
- APK path (`/system`, `/system_ext`, `/product`, `/vendor`, `/data/app`, etc.).
- Whether it is a preloaded system package or user-installed app.
- Services, receivers, providers and activities.
- Requested permissions versus permissions actually granted.
- Whether the package is enabled for user 0.
- Whether other system components appear to depend on it.

A declared permission is not proof that it was granted or used. A service declaration is not proof that the service is continuously running.

---

## 4. Decide whether a package is a candidate

Before removal, answer all of these:

- Can I identify the package's purpose from reliable device-specific evidence?
- Is it an optional app rather than a core operating-system component?
- Does the device still work normally without it?
- Have I checked whether another Xiaomi service depends on it?
- Do I have the exact package name, not just a display name?
- Do I know how to restore it?
- Have I recorded the current firmware build and the evidence?

If any answer is unknown, **do not remove it yet**. Mark it `investigate` in the repository.

### Never treat these as ordinary bloatware

Do not casually remove or disable packages involved in:

- Android System UI, Settings, System framework or Package Installer.
- Permission Controller, security and device policy.
- Telephony, SIM, SMS, emergency calling or carrier configuration.
- Wi-Fi, Bluetooth, NFC, mobile data, VPN or network connectivity.
- Launcher, input methods, accessibility or notification handling.
- Power management, battery management, thermal monitoring or hardware protection.
- Firmware updates, boot, recovery, encryption or storage.
- Google Play services or other dependencies without understanding their role.

A package name containing words such as `analytics`, `daemon`, `service`, `system` or `security` does not by itself establish whether it is safe to remove.

---

## 5. Apply a change — one package at a time

### First attempt: disable (only if appropriate)

```powershell
.\adb.exe shell pm disable-user --user 0 PACKAGE_NAME
```

This requests that Package Manager disable the package for user 0. HyperOS may reject the operation for protected system packages. A rejection is not a reason to try root or force-delete the APK.

### User-level uninstall

For a package that has been investigated and approved for removal:

```powershell
.\adb.exe shell pm uninstall --user 0 PACKAGE_NAME
```

Replace `PACKAGE_NAME` with the exact package name. Do not paste the placeholder literally.

This changes the installed state for user 0. It does not necessarily remove the package from the firmware image, and it does not guarantee that all related services or data collection are gone.

### Verify immediately

```powershell
.\adb.exe shell pm list packages | findstr /i "PACKAGE_NAME"
```

No matching output means the package was not listed by that query for the current user at that time. It does not prove that the APK is erased from the system image.

Also inspect the device manually:
- Does the app icon disappear?
- Do Settings and normal navigation still work?
- Do calls, mobile data, Wi-Fi, Bluetooth and notifications work?
- Do camera, gallery, sharing, location and app installation still work?
- Are there new crashes, boot delays, missing menus or repeated errors?

Do not proceed to another package until the device passes the relevant checks.

---

## 6. Restore a package if needed

If a preloaded package was removed only for user 0 and remains available in the system image, try:

```powershell
.\adb.exe shell cmd package install-existing --user 0 PACKAGE_NAME
```

Then verify:

```powershell
.\adb.exe shell pm list packages | findstr /i "PACKAGE_NAME"
```

Some builds may require a different supported Package Manager syntax. If the command fails, preserve the exact error and investigate the build-specific recovery path. Do not download random APKs or use a package from a different firmware build.

A reboot may be appropriate after a change, but rebooting is not a substitute for verifying the package state.

---

## 7. Review Xiaomi privacy controls separately

Some privacy settings are not package-removal operations.

On the investigated Redmi K50i, the user reported:
- Xiaomi **MSA** authorization revoked.
- Xiaomi **Feedback** authorization revoked.
- **Usage & Diagnostics** switched off.

These settings should be documented as privacy-control changes, not as ADB-uninstalled apps. The Google package `com.google.android.feedback` is separate from Xiaomi's Feedback authorization setting.

Menu names and availability can vary by firmware and region.

---

## 8. Finish the ADB session securely

When finished:

1. Disconnect the USB cable.
2. Turn off **USB debugging** in Developer options.
3. Use **Revoke USB debugging authorizations** if the phone offers that option.
4. Keep the before/after package lists and command output in a private research folder.
5. Redact serial numbers, account identifiers, email addresses, phone numbers and other personal data before publishing evidence.

---

## 9. What this workflow does not do

- It does not root the phone or modify read-only system partitions.
- It does not guarantee removal of every Xiaomi app or background component.
- It does not prove that all telemetry has stopped.
- It does not securely erase deleted files from physical NAND storage.
- It does not make a package safe to remove merely because another user removed it.
- It does not provide a universal package-removal list for all Xiaomi devices.

**Research principle:** publish the command, device/build, observed result, limitations and recovery method together. A debloat guide is only useful when another user can understand both the benefit and the risk.
