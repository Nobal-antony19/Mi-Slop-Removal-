# 06 — Safe Debloating & Recovery

## What debloating means
Debloating removes or disables unwanted apps/features. Some preinstalled packages are optional; others support important firmware functions. Never remove a package just because its name is unfamiliar.

## Preferred order
1. Turn off the feature in device settings.
2. Revoke an unnecessary permission through Android settings.
3. Disable a user-facing app through Settings if supported.
4. Consider user-level removal only after researching dependencies and impact.

## Before changing anything
Record model/build, package name, APK path, metadata, permissions, components, reason for removal, potential impact and recovery method.

## User-level uninstall
Only for a positively identified package you deliberately choose to remove:
```powershell
.\adb.exe shell pm uninstall --user 0 PACKAGE_NAME
```
This generally removes the package for Android user 0. It does not delete the APK from the system image and may break dependent features.

## Verify
```powershell
.\adb.exe shell pm list packages | findstr /i "PACKAGE_NAME"
.\adb.exe shell pm list packages -u | findstr /i "PACKAGE_NAME"
```
The second command includes uninstalled package records.

## Restore where supported
If the system image retains the package and firmware permits:
```powershell
.\adb.exe shell cmd package install-existing PACKAGE_NAME
.\adb.exe shell pm list packages | findstr /i "PACKAGE_NAME"
```
Restoration is not guaranteed.

## Treat as high risk
Do not blindly remove System UI, Settings, launcher, package installer, permission controller, Security Center/Core, power/thermal management, telephony/connectivity, Xiaomi framework/push services or Google Play services.

## Change-control checklist
Is the package correctly identified? Is the reason clear? Does the device/build match? Have dependencies been investigated? Is rollback known? Is data backed up? Can the result be verified?

There is no universal safe Xiaomi debloat list.
