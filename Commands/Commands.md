# Xiaomi ADB Command Library — Copy, Paste, Verify

> **For:** Xiaomi / Redmi / POCO devices running MIUI or HyperOS  
> **Computer:** Windows PowerShell + Android Platform Tools  
> **Goal:** Fast, copy-paste commands for ADB checks, device inventory, app/service discovery, network diagnostics, logging and Xiaomi ads/privacy controls.

**Read this first:** Commands are grouped by risk. Sections 1–5 are read-only diagnostics. Section 6 changes a system setting. Section 7 changes package state. Do not paste the entire file as one script. Run one section at a time and check the result.

Package names, menus and available shell utilities vary by model, region, Android version and firmware build. This is not a universal debloat list.

---

## 0. Open PowerShell in Platform Tools

Change this path if your `platform-tools` folder is somewhere else:

```powershell
cd "$env:USERPROFILE\Downloads\platform-tools-latest-windows\platform-tools"
```

Check that `adb.exe` is present:

```powershell
.\adb.exe version
```

---

# 1. First three — ADB handshake and debugging test

### 1.1 Detect the connected phone

```powershell
.\adb.exe devices -l
```

[Explanation: Lists devices visible to ADB. The phone should show the status `device`. `unauthorized` means unlock the phone and approve the USB debugging prompt. `offline` usually means reconnect the cable, restart ADB or change the USB port.]

### 1.2 Test a command through the ADB shell

```powershell
.\adb.exe shell echo ADB_HANDSHAKE_OK
```

[Explanation: Sends a harmless command to Android and prints a confirmation string if the shell connection works. Expected output: `ADB_HANDSHAKE_OK`.]

### 1.3 Check Android has completed booting

```powershell
.\adb.exe shell getprop sys.boot_completed
```

[Explanation: Reads Android's boot-completion property. Output `1` normally means the Android boot process has completed. A blank value or another value can mean the device is still booting or the property is unavailable.]

**Pass condition:** the phone is listed as `device`, the echo test returns `ADB_HANDSHAKE_OK`, and boot completion returns `1`.

---

# 2. Android, HyperOS and ADB version

### 2.1 ADB version on the computer

```powershell
.\adb.exe version
```

[Explanation: Displays the Android Debug Bridge version installed on Windows. This is the computer-side ADB version, not the phone's Android version.]

### 2.2 Android version

```powershell
.\adb.exe shell getprop ro.build.version.release
```

[Explanation: Prints the Android release version reported by the phone.]

### 2.3 Android SDK level

```powershell
.\adb.exe shell getprop ro.build.version.sdk
```

[Explanation: Prints the Android API level. Useful when comparing command behaviour across Android releases.]

### 2.4 Build fingerprint

```powershell
.\adb.exe shell getprop ro.build.fingerprint
```

[Explanation: Prints the firmware build fingerprint. It helps identify the exact software build. **Do not publish device identifiers or personal information from diagnostic output without checking and redacting them.**]

### 2.5 Xiaomi build properties

```powershell
.\adb.exe shell getprop | findstr /i "miui hyperos incremental version region"
```

[Explanation: Filters Android system properties for likely Xiaomi OS/build/region fields. Property names vary; an empty or incomplete result is not proof that the phone lacks HyperOS.]

---

# 3. Package and app inventory

### 3.1 List all packages visible to the current user

```powershell
.\adb.exe shell pm list packages
```

[Explanation: Lists package identifiers known to Package Manager for the current user. This is the main inventory to use before investigating apps.]

### 3.2 List system packages

```powershell
.\adb.exe shell pm list packages -s
```

[Explanation: Lists packages classified as system packages. System package does not mean unnecessary or safe to remove.]

### 3.3 List third-party packages

```powershell
.\adb.exe shell pm list packages -3
```

[Explanation: Lists third-party packages. This is useful for finding user-installed apps, but some preloaded apps may not appear here.]

### 3.4 Filter Xiaomi-related package names

```powershell
.\adb.exe shell pm list packages | findstr /i "miui xiaomi"
```

[Explanation: Filters package identifiers containing `miui` or `xiaomi`. It will miss Xiaomi packages with other naming patterns and may include components that are essential.]

### 3.5 Filter common advertising / analytics naming patterns

```powershell
.\adb.exe shell pm list packages | findstr /i "msa analytics adservice tracking"
```

[Explanation: A discovery filter only. It does not identify every ad or analytics component and does not mean every match is safe to remove.]

### 3.6 Save package inventory to a text file

```powershell
.\adb.exe shell pm list packages | Out-File -Encoding utf8 .\packages-inventory.txt
```

[Explanation: Saves the current package list in the current PowerShell directory. Keep this as a baseline before making changes.]

### 3.7 Save system and third-party inventories separately

```powershell
.\adb.exe shell pm list packages -s | Out-File -Encoding utf8 .\system-packages.txt
.\adb.exe shell pm list packages -3 | Out-File -Encoding utf8 .\third-party-packages.txt
```

[Explanation: Creates two local text files for easier review. These files are generated on the computer and do not change the phone.]

---

# 4. Running services and service discovery

### 4.1 List Android binder services

```powershell
.\adb.exe shell service list
```

[Explanation: Lists services registered with Android's service manager. This is a service-registry view, not a list of every Linux process or every running app.]

### 4.2 List dumpable system services

```powershell
.\adb.exe shell dumpsys -l
```

[Explanation: Lists services that can be queried using `dumpsys`. Useful for discovering service names before inspecting one.]

### 4.3 Inspect a specific service

```powershell
.\adb.exe shell dumpsys SERVICE_NAME
```

[Explanation: Replace `SERVICE_NAME` with a name returned by `dumpsys -l`, such as `batterystats` or another relevant service. Output can be very large and may contain sensitive details.]

### 4.4 Search process list for a keyword

```powershell
.\adb.exe shell ps -A | findstr /i "KEYWORD"
```

[Explanation: Filters the process list for a keyword. Replace `KEYWORD` with a process-name fragment. A process being present does not automatically mean it is harmful or unnecessary.]

---

# 5. Network status and accounting

> Android builds differ in which network tools are included and what an unprivileged ADB shell is allowed to see. These commands are diagnostics, not proof of what data an app transmitted.

### 5.1 Show network interfaces and addresses

```powershell
.\adb.exe shell ip addr
```

[Explanation: Displays network interfaces and assigned addresses. Useful for checking Wi-Fi, mobile and loopback interfaces.]

### 5.2 Show routing table

```powershell
.\adb.exe shell ip route
```

[Explanation: Displays IPv4 routes known to the device. It can help diagnose the active default route.]

### 5.3 Show socket connections (if supported)

```powershell
.\adb.exe shell ss -tun
```

[Explanation: Attempts to show TCP and UDP sockets. Some Android builds do not include `ss`, or restrict the details visible to the shell user. If unavailable, record the error rather than installing random binaries.]

### 5.4 Legacy netstat (if supported)

```powershell
.\adb.exe shell netstat -tun
```

[Explanation: Attempts to show network sockets using the legacy `netstat` utility. It may not be installed on modern Android builds. It is not the same as Android's historical per-UID traffic accounting.]

### 5.5 Android network usage accounting

```powershell
.\adb.exe shell dumpsys netstats
```

[Explanation: Dumps Android's network statistics service. Depending on the build and permissions, it may include interfaces, time buckets and UID-level byte counters. It is accounting data, not a packet capture: it does not show payload contents or prove that traffic was telemetry.]

### 5.6 Save network accounting output

```powershell
.\adb.exe shell dumpsys netstats | Out-File -Encoding utf8 .\netstats-report.txt
```

[Explanation: Saves the network statistics output locally for later comparison. Redact sensitive identifiers before sharing.]

---

# 6. Logging — inspect, capture and stop your own capture

**Important:** There is no universal, safe, non-root ADB command to turn off all Android/HyperOS system logging. System logs are used for diagnostics and reliability. Do not publish a command that claims to disable all logging when it only clears a buffer or stops a capture process.

### 6.1 Read recent log entries

```powershell
.\adb.exe logcat -d -t 300
```

[Explanation: Dumps a limited number of recent log entries and exits. Logs can contain tokens, account names, phone numbers, app activity and other private data. Inspect locally and redact before sharing.]

### 6.2 Capture logs to a local file

```powershell
.\adb.exe logcat -v time > .\device-logcat.txt
```

[Explanation: Starts a live log capture and writes output to a file on the Windows computer. Stop this capture with `Ctrl+C` in the same PowerShell window. This stops the capture command; it does not disable Android's internal logging.]

### 6.3 Stop a live logcat capture

Press:

```text
Ctrl + C
```

[Explanation: Stops the foreground `adb logcat` process in that PowerShell window. It does not change the phone's logging configuration.]

### 6.4 Clear the current logcat buffers (use only when intentional)

```powershell
.\adb.exe logcat -c
```

[Explanation: Clears the accessible logcat buffers. This deletes diagnostic evidence from those buffers; it does not turn logging off. Do not run before collecting evidence or when troubleshooting an issue.]

---

# 7. MSA and Xiaomi ads / recommendations

> **Caution:** Xiaomi package names vary by region and build. Confirm that the package exists on your own phone before changing it. Removing MSA does not remove every ad, recommendation or promotional surface in MIUI/HyperOS.

### 7.1 Check whether the global MSA package is present

```powershell
.\adb.exe shell pm list packages | findstr /i "com.miui.msa"
```

[Explanation: Checks whether a package matching the MSA identifier appears in the current user's package list. The common global package identifier is `com.miui.msa.global`, but do not assume it exists on every region/build.]

### 7.2 Inspect MSA before changing it

```powershell
.\adb.exe shell dumpsys package com.miui.msa.global
```

[Explanation: Shows package metadata if this exact package exists. If Package Manager reports that the package is unknown, do not substitute a guessed name.]

### 7.3 Attempt user-level MSA removal (state-changing)

```powershell
.\adb.exe shell pm uninstall --user 0 com.miui.msa.global
```

[Explanation: Attempts to uninstall the identified global MSA package for Android user 0. Use only after confirming the exact package exists and reviewing its role on the specific device. This does not erase the APK from the firmware image or guarantee that all Xiaomi ads disappear. If the command fails, preserve the error; do not escalate to root or system-partition deletion.]

### 7.4 Verify MSA package visibility

```powershell
.\adb.exe shell pm list packages | findstr /i "com.miui.msa"
```

[Explanation: Checks whether the package is still listed after the operation. No match means it is not listed for the queried user at that moment; it does not prove firmware-level deletion.]

### 7.5 Restore MSA if the preloaded package is still available

```powershell
.\adb.exe shell cmd package install-existing --user 0 com.miui.msa.global
```

[Explanation: Attempts to restore the preloaded package for user 0. This works only if the package remains available to Package Manager on that build.]

### 7.6 Turn off Xiaomi ad recommendations manually

On the phone, review the privacy/advertising controls available in Settings and the individual Xiaomi apps. Common places include:
- Settings → Passwords & security / Privacy → Authorization & revocation → MSA (wording varies).
- Settings → Privacy → Ad services / Personalized ad recommendations (if present).
- Individual Xiaomi apps such as Security, Themes, Downloads, File Manager, Music and Video may have their own **Recommendations**, **Receive recommendations** or **Personalized services** toggles.

[Explanation: These controls are firmware- and app-version-dependent. Turning off a recommendation toggle can reduce personalized promotions in that app; it is not equivalent to uninstalling MSA or disabling every ad-related service.]

---

# 8. Optional Private DNS ad blocking

This uses Android's Private DNS setting with AdGuard's public DNS hostname. It is a device-wide setting and may affect DNS resolution, captive portals, work/school networks or apps that use their own DNS/connection methods.

### 8.1 Set Private DNS to AdGuard DNS (state-changing)

```powershell
.\adb.exe shell settings put global private_dns_mode hostname
.\adb.exe shell settings put global private_dns_specifier dns.adguard-dns.com
```

[Explanation: Configures Android Private DNS to use the hostname `dns.adguard-dns.com`. This can block many domains classified by the DNS provider as advertising or tracking domains. It is not a complete ad blocker: it cannot reliably remove ads served from the same domain as content, and apps may use other DNS or networking methods.]

### 8.2 Check the configured Private DNS values

```powershell
.\adb.exe shell settings get global private_dns_mode
.\adb.exe shell settings get global private_dns_specifier
```

[Explanation: Reads the stored Private DNS mode and hostname. A stored value does not by itself prove that the DNS connection is currently successful.]

### 8.3 Restore Private DNS to automatic

```powershell
.\adb.exe shell settings put global private_dns_mode opportunistic
.\adb.exe shell settings delete global private_dns_specifier
```

[Explanation: Returns Private DNS to opportunistic/automatic mode and removes the custom hostname. If connectivity breaks after enabling a custom provider, use this restore section or set Private DNS to Automatic in Android Settings.]

### 8.4 If DNS or Wi-Fi stops working

1. Run the restore commands above.
2. Toggle Wi-Fi off and on.
3. Check whether the network requires a captive portal sign-in.
4. If this is a managed work/school device, follow the administrator's DNS policy.

---

# 9. End the ADB session securely

After you finish:

1. Disconnect the USB cable.
2. Turn off USB debugging.
3. Revoke USB debugging authorizations if the option is available.
4. Keep diagnostic output private until it has been reviewed and redacted.

---

## Quick risk index

| Section | Changes the phone? | Risk |
|---|---:|---|
| 1. ADB handshake | No | Low |
| 2. Version/build info | No | Low |
| 3. Package inventory | No | Low |
| 4. Service discovery | No | Low |
| 5. Network diagnostics | No | Low |
| 6. Logcat read/capture | No configuration change | Privacy risk in output |
| 6.4 Clear logcat buffers | Yes, deletes log evidence | Use carefully |
| 7. MSA user-level uninstall | Yes | Medium; device/build dependent |
| 8. Private DNS | Yes | Medium; can affect connectivity |

**Project rule:** Every state-changing command must include a verification command and a recovery path wherever one exists. Never label a package “safe” for every Xiaomi device based on one Redmi K50i test.
