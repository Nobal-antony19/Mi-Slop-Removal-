# 05 — Telemetry, Logs & Network Analysis

## Purpose
Investigate analytics-related metadata, Android logs and available network accounting without claiming more than the evidence supports.

## Package metadata
```powershell
.\adb.exe shell pm path com.miui.analytics
.\adb.exe shell dumpsys package com.miui.analytics
```
Review permissions, services and providers. OneTrack-related names are useful research leads but do not reveal transmitted payload contents.

## Logcat
```powershell
.\adb.exe logcat -d -t 300
```
This requests a limited recent buffer on supported builds. Logs may contain sensitive information. Do not publish raw logs without review and redaction.

## Network accounting
```powershell
.\adb.exe shell dumpsys netstats
.\adb.exe shell dumpsys package PACKAGE_NAME
```
Counters may show bytes associated with a UID/interface over an accounting period. They generally do not identify payload, destination, user intent or whether traffic was analytics. UID mapping, shared services and accounting windows complicate interpretation.

## Record evidence
Capture the exact command, date/time, UID mapping method, counter interval, device activity state and limitations.

## Common mistakes
- Internet permission does not prove upload.
- Network bytes do not prove telemetry.
- A declared service does not prove it is running.
- A running service does not prove a particular data collection event.
- Removing one analytics package does not prove all telemetry pathways are disabled.

Stronger network analysis may require authorised packet-level or endpoint-level investigation. Consider TLS encryption, DNS, shared services and privacy. Investigate only devices and networks you are authorised to examine.
