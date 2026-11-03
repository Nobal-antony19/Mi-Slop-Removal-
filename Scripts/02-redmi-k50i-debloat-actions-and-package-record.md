# Redmi K50i — Debloat Actions and Package Record

> **Device case study:** Redmi K50i running HyperOS, investigated from Windows 11 using Android Platform Tools (ADB).
>
> **Evidence standard:** This file separates actions confirmed in the investigation from packages/settings that were only inspected or changed through Settings. Do not interpret every item below as an app uninstall.

---

## 1. Test environment

| Field | Recorded value |
|---|---|
| Device | Redmi K50i |
| OS | HyperOS |
| Computer OS | Windows 11 |
| Interface | Android Debug Bridge (ADB) |
| Root access | Not used |
| Bootloader unlock | Not used |
| Factory reset | Not performed |
| ADB folder used | `C:\\Users\\nobel\\Downloads\\platform-tools-latest-windows\\platform-tools` |

The exact HyperOS build number, region and Android version were not recorded in the available investigation notes. Add them before treating the results as reproducible on another device.

---

## 2. Confirmed package action: Xiaomi Analytics

### Package

```text
com.miui.analytics
```

### What we inspected

The package dump showed OneTrack-related components, including:

- `OneTrackService`
- `TrackService`
- `AppenderService`
- `AnalyticsService`
- `OneTrackProvider`
- `AnalyticsProvider`
- `DumpLogProvider`

The package metadata included internet access and permission declarations/capabilities relating to usage statistics, phone state and location.

**Interpretation limit:** declarations do not establish that each permission was granted, that each component was active, or that particular data was uploaded.

### Command attempted first: disable package

```powershell
.\adb.exe shell pm disable-user --user 0 com.miui.analytics
```

**Observed result:** HyperOS rejected the request with a `SecurityException` stating that the system package could not be disabled using this operation on the tested build.

### Command that succeeded: uninstall for user 0

```powershell
.\adb.exe shell pm uninstall --user 0 com.miui.analytics
```

**Observed result:** the command completed successfully.

### Verification

```powershell
.\adb.exe shell pm list packages | findstr /i "com.miui.analytics"
```

**Observed result:** no matching output was returned.

### What we can conclude

- The package was no longer listed by the package-list query for user 0 after the command.
- The user-level uninstall operation succeeded on this Redmi K50i/HyperOS setup.

### What we cannot conclude

- The APK was physically erased from the firmware/system image.
- Every Xiaomi analytics or telemetry component was removed.
- All data collection or network communication by Xiaomi stopped.
- The package's previous network counters represented telemetry payloads.

### Recovery command (if needed)

If the preloaded package remains available to Package Manager:

```powershell
.\adb.exe shell cmd package install-existing --user 0 com.miui.analytics
```

Verify after attempting restoration:

```powershell
.\adb.exe shell pm list packages | findstr /i "com.miui.analytics"
```

---

## 3. Xiaomi privacy settings changed through Settings

These were reported as completed by the device owner. They are **not ADB package uninstalls**.

| Item | Action reported | How to describe it accurately |
|---|---|---|
| MSA authorization | Revoked | Xiaomi authorization/revocation setting changed |
| Feedback authorization | Revoked | Xiaomi authorization/revocation setting changed |
| Usage & Diagnostics | Turned off | Diagnostic sharing setting switched off |

Menu paths and wording may differ between HyperOS versions and regions. These actions do not prove that every Xiaomi service or data flow is disabled.

### Do not confuse Xiaomi Feedback with Google Feedback

```text
com.google.android.feedback
```

This is a separate Google Feedback package. Its presence is not proof that Xiaomi Feedback authorization remains enabled, and revoking Xiaomi Feedback authorization does not mean this Google package was uninstalled.

No confirmed ADB uninstall of `com.google.android.feedback` is recorded in this case study.

---

## 4. Inspected package: Xiaomi Bug Report

### Package

```text
com.miui.bugreport
```

### Observed package path

```text
/product/app/MiBugReportOS2Global/MiBugReportOS2Global.apk
```

### Why it was investigated

The device owner reported seeing a feedback/report-problem banner after pressing volume buttons or shaking the phone.

### What the path tells us

The APK was present under the product partition in the inspected firmware.

### What it does not tell us

- It does not prove continuous background uploading.
- It does not prove that this package generated the observed banner.
- It does not establish what information, if any, was transmitted.

### Current status

**Inspected; no confirmed removal or disable operation.**

To identify the banner's sender, wait for it to appear, long-press the notification and inspect the app name shown by Android. Do not disable System UI or other core components based only on the banner's appearance.

---

## 5. Network and AppOps observations

These are investigation observations, not proof of a successful privacy outcome.

### Network accounting observed for the analytics UID

| Counter | Recorded value |
|---|---:|
| RX | 541,609 bytes (about 0.52 MiB) |
| TX | 1,004,537 bytes (about 0.96 MiB) |

These counters were captured for a period associated with UID `10095`.

**Interpretation limit:** byte counters do not reveal the contents, destination, purpose or timing of individual transfers. They do not prove that the traffic was telemetry.

### AppOps snapshot

The captured output showed:
- `READ_PHONE_STATE`: ignored
- `GET_ACCOUNTS`: ignored
- `RUN_ANY_IN_BACKGROUND`: allowed

AppOps output is a point-in-time policy/state view. It does not prove whether a permission was used earlier or whether data was transmitted.

### Service observations

OneTrack-related services appeared in service output and were shown as active/bound to system or Xiaomi components during the inspection.

A service being listed or bound does not, by itself, prove that it uploaded data or reveal the content of any communication.

---

## 6. Package/action status at a glance

| Package or setting | Evidence in this investigation | Status |
|---|---|---|
| `com.miui.analytics` | User-level uninstall command succeeded; package-list query returned no match | **Removed for user 0 and verified by package-list query** |
| `com.miui.msa.global` | Package was present | **Presence observed; no confirmed uninstall** |
| `com.miui.bugreport` | Package path under `/product/app/` observed | **Inspected; no confirmed removal** |
| `com.google.android.feedback` | Identified as a separate Google package | **No confirmed uninstall** |
| MSA authorization | User reported revoking it in Settings | **Setting changed** |
| Xiaomi Feedback authorization | User reported revoking it in Settings | **Setting changed** |
| Usage & Diagnostics | User reported switching it off | **Setting changed** |

**Important:** Do not add a package to the “removed” list unless there is a recorded command and a verification result. If more packages were removed in a separate session, add their exact package names and command output here before publishing.

---

## 7. Safe next steps for expanding this device-specific list

For each additional app the owner wants to investigate:

1. Find its exact package name from the device, not from an online debloat list.
2. Capture `pm path PACKAGE_NAME`.
3. Capture `dumpsys package PACKAGE_NAME`.
4. Identify what the app does and whether it is a core dependency.
5. Record the evidence and risk assessment.
6. If approved, remove only that package for user 0.
7. Verify the package-list result.
8. Test device functions and record any side effects.
9. Document the restore command before publishing the change.

Useful read-only commands:

```powershell
.\adb.exe shell pm list packages
.\adb.exe shell pm list packages -3
.\adb.exe shell pm path PACKAGE_NAME
.\adb.exe shell dumpsys package PACKAGE_NAME
```

Do not run bulk removal commands against guessed package names. Do not disable thermal services, power management, System UI, Package Installer, permission controller, telephony, security or update components as a generic debloating step.

---

## 8. Publication checklist

Before committing this case study to a public GitHub repository:

- [ ] Add the exact HyperOS build and Android version.
- [ ] Add the device region if known.
- [ ] Redact serial numbers, account IDs, phone numbers and private paths/log contents where appropriate.
- [ ] Preserve raw command output privately so claims can be checked.
- [ ] Distinguish observed facts from interpretation.
- [ ] Do not label MSA or Bug Report as removed without evidence.
- [ ] Do not claim all telemetry was stopped.
- [ ] Include the recovery command for every package actually removed.

**Case-study conclusion:** The investigation confirms one Xiaomi package (`com.miui.analytics`) was uninstalled for user 0 and no longer appeared in the subsequent package-list query. It also records three Xiaomi privacy-setting changes and inspection of other packages. It does not establish that every Xiaomi app was removed or that all telemetry ceased.
