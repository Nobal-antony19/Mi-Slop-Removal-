# 09 — Redmi K50i Research Case Study

## Scope
This case study records observations from one Redmi K50i running HyperOS, investigated with Windows PowerShell and ADB without root or bootloader unlocking. Exact firmware build, Android version and security patch should be captured from a fresh baseline before reproduction.

This is a single-device case study, not a universal Xiaomi finding.

## Finding A — `com.miui.analytics`

### Observed
- Package metadata exposed OneTrack-related services/providers, including `OneTrackService`, `TrackService`, `AppenderService`, `AnalyticsService`, `OneTrackProvider`, `AnalyticsProvider` and `DumpLogProvider`.
- The package declared Internet access and other permissions/capabilities.
- A `pm disable-user` attempt was rejected with a security exception.
- This command was accepted:
```powershell
.\adb.exe shell pm uninstall --user 0 com.miui.analytics
```
- A subsequent package-list check returned no matching installed package for the current user.

### Interpretation
Metadata supports classifying the package as analytics/OneTrack-related. The user-level uninstall removed it for user 0 on the tested build.

### Not established
Whether a payload was uploaded, what it contained, whether all Xiaomi telemetry stopped, or whether another component provides overlapping diagnostics.

## Finding B — Network accounting
A captured UID accounting snapshot showed approximately 541,609 bytes received and 1,004,537 bytes transmitted for UID 10095 during the captured period.

This establishes recorded network bytes for that UID/accounting context. It does not establish payload, destination, process attribution for every byte or telemetry content.

## Finding C — Xiaomi privacy settings
Observed: MSA authorization was revoked, Feedback authorization was revoked, and Usage & Diagnostics was switched off.

Not established: that every related service or collection pathway was disabled, or that Android, Google and other apps stopped all diagnostics.

## Finding D — `com.miui.bugreport`
Observed package path:
```text
/product/app/MiBugReportOS2Global/MiBugReportOS2Global.apk
```
Its presence does not prove that it generated a particular feedback banner or continuously uploads logs. Identify a banner's sender by long-pressing the notification.

## Finding E — Storage
Observed: `/data` used F2FS; mount options included `discard`, `background_gc=on` and `inlinecrypt`.

These options are consistent with discard support, background garbage collection and inline encryption support/use in the reported mount context. They do not prove physical erasure of old NAND pages, unrecoverability of a specific file or the exact encryption-key lifecycle.

## Overall conclusion
The investigation demonstrated ADB inspection, package metadata analysis, permission/AppOps review, settings review and careful interpretation of network and filesystem evidence. It did not establish complete telemetry elimination or forensic secure deletion. These limitations are part of the result.

## Reproduction details to add
Record exact model/variant, region/ROM, Android version, HyperOS build, security patch, ADB version, test date, sanitized command outputs and sources.
