# 07 — Storage, Cache & Data Erasure

## Cache versus app data
Cache usually contains temporary files an app can recreate. App data can include settings, databases, accounts, downloads and persistent content. Clearing app data may sign a user out or delete local content.

## Clear cache
Where available: Settings → Apps → Manage apps → select app → Storage → Clear cache. Menu wording varies. Do not select Clear data unless you understand the consequences.

## ADB warning
```powershell
.\adb.exe shell pm clear PACKAGE_NAME
```
This clears app data for the relevant user. It is not a cache-only command. There is no reliable universal ADB command that clears only every app cache across Xiaomi/HyperOS builds.

## F2FS and flash storage
F2FS is designed for flash storage. Android may use discard/TRIM and background garbage collection. The flash translation layer maps logical addresses to physical flash pages; wear-levelling and garbage collection can move data. Userland commands generally cannot prove every old physical NAND page has been overwritten.

## TRIM/discard
Discard indicates that logical blocks are no longer needed by the filesystem. It can help storage management but does not prove physical erasure of every NAND page. Mount options such as `discard` and `background_gc=on` describe configuration, not the recoverability of a particular deleted file.

## Encryption
Android commonly uses file-based encryption, but implementation and key lifecycle are device/build dependent. An encryption-related mount option alone does not prove deletion immediately destroys every key or makes all old pages unrecoverable.

## Responsible conclusions
You may report that a file is no longer visible through ordinary browsing, that the filesystem reports discard options, or that a storage operation completed if output confirms it. Do not claim every NAND page was erased or a particular file is forensically unrecoverable.

Avoid raw block writes, `dd`, random full-storage filling, root-only erasure scripts and unknown secure-delete apps.

The Redmi K50i case study did not establish forensic secure deletion. That limitation must remain explicit.
