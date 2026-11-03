# 03 — Xiaomi System & Package Analysis

## Why package analysis matters
A Xiaomi phone contains Android framework components, Google components, Xiaomi services, carrier components and user-installed apps. A package name alone does not establish whether it is optional, malicious or safe to remove.

## Package inventory
```powershell
.\adb.exe shell pm list packages
.\adb.exe shell pm list packages -3
.\adb.exe shell pm list packages | findstr /i "miui xiaomi"
```
The last command is only a discovery filter; not every Xiaomi component has an obvious prefix.

## Inspect one package
Replace `PACKAGE_NAME` with the exact identifier:
```powershell
.\adb.exe shell pm path PACKAGE_NAME
.\adb.exe shell dumpsys package PACKAGE_NAME
.\adb.exe shell pm list packages | findstr /i "PACKAGE_NAME"
```

## Package components
- **Activities:** user-facing screens or entry points.
- **Services:** components that can perform work without a visible screen.
- **Broadcast receivers:** respond to broadcast events.
- **Content providers:** expose structured data to authorised components.
- **Permissions:** describe requested or granted access.

A declared service does not prove it is currently running.

## Examples from one Redmi K50i investigation
| Package | Research context | Caution |
|---|---|---|
| `com.miui.analytics` | OneTrack/analytics-related metadata observed | Removal does not prove all telemetry stops |
| `com.miui.msa.global` | Xiaomi advertising-related package name | Check build-specific settings/dependencies |
| `com.miui.bugreport` | Bug-report component | Presence does not prove continuous upload |
| `com.miui.securitycenter` | Security Center | Treat as core |
| `com.miui.securitycore` | Security-related component | Treat as core |
| `com.miui.powerkeeper` | Power management | May affect battery/background behaviour |
| `com.xiaomi.xmsf` | Xiaomi framework-related component | May have broad dependencies |
| `com.xiaomi.xmsfkeeper` | Xiaomi framework-related component | Do not casually remove |

These are research examples, not a universal removal list.

## Investigation questions
What is the package purpose according to reliable evidence? Where is its APK? What permissions and components are declared? What feature may depend on it? Is there a recovery path? What was actually tested on this exact build?

Never recommend removal based only on an unfamiliar name or a debloat list for another device.
