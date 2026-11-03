# 04 — Permissions, AppOps & Privacy Controls

## Terms
- **Requested permission:** declared by an app in package metadata.
- **Granted permission:** permission Android has granted.
- **AppOps:** Android framework controls/records for selected app operations; modes may include allowed, ignored, errored or default.

These concepts are related but not interchangeable.

## Inspect permissions
```powershell
.\adb.exe shell dumpsys package PACKAGE_NAME
```
Review requested permissions, install permissions, runtime permissions and component declarations. Formatting varies by Android version.

## Inspect AppOps
```powershell
.\adb.exe shell cmd appops get PACKAGE_NAME
```
For one operation where supported:
```powershell
.\adb.exe shell cmd appops get PACKAGE_NAME OPERATION
```

## Interpret carefully
| Evidence | Can indicate | Does not prove |
|---|---|---|
| `INTERNET` declared | Package can request network access | It connected or what it sent |
| Location permission requested | Location capability declared | Location was accessed |
| `READ_PHONE_STATE` ignored | Operation ignored in shown context | No other device information is available |
| Background operation allowed | Background operation may be permitted | App is actively running |
| Service declared | Package contains a service | Service is active |

## Xiaomi privacy settings
Depending on firmware, look for Authorization & revocation, Usage & diagnostics, Personalized ads, User Experience Program, Feedback/Report a problem and app privacy settings.

Record the exact setting label, state and date. Revoking a vendor authorization is not proof that every related service or data pathway is disabled. Turning off Usage & Diagnostics does not prove all Android, Google, app or firmware diagnostics stop. Ad personalisation controls do not necessarily remove all ads.

## Safe workflow
Identify the package/setting, record its original state, understand feature dependencies, prefer supported Android settings, change one item at a time, verify and record side effects. Avoid broad permission-revocation scripts for system packages.
