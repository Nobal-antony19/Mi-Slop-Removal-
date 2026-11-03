# Xiaomi Privacy & Security Research Toolkit

A Xiaomi-focused, evidence-led Android privacy and security research portfolio built around ADB and Windows PowerShell.

This repository documents how to inspect a Xiaomi phone, understand selected system packages and permissions, review diagnostic and analytics settings, investigate network and log evidence, and make carefully scoped changes where the device permits them.

> **Important:** This is a research and education project, not an official Xiaomi tool, not a malware scanner, and not a guarantee that telemetry is fully disabled or deleted files are unrecoverable.

## Project goals

- Explain Xiaomi/MIUI/HyperOS privacy and diagnostic components in plain language.
- Demonstrate safe ADB-based device inspection.
- Record evidence before making a change.
- Separate observations, interpretations, and conclusions.
- Explain risks, rollback options, and device/build differences.
- Help other Xiaomi users make informed decisions without blindly copying debloat lists.

## Who this is for

- Xiaomi, Redmi, and POCO users who want to understand their device.
- Android learners exploring ADB, package management, permissions, AppOps, logs, and network accounting.
- Junior security analysts building a small, reproducible mobile-device research portfolio.

## Scope

**In scope**
- Xiaomi, Redmi, and POCO Android devices running MIUI or HyperOS.
- Windows 10/11 PowerShell and Android Platform Tools (ADB).
- Package inventory and package metadata.
- Permissions and AppOps inspection.
- Xiaomi authorization/diagnostic settings.
- Read-only log and network-accounting inspection.
- Per-package user-level removal where supported and justified.
- Storage, cache, trim, encryption, and deleted-data limitations.
- Evidence capture, reporting, and rollback documentation.

**Out of scope**
- Samsung, Motorola, OnePlus, Pixel, or other OEM-specific package lists.
- iPhone/iOS.
- Rooting, bootloader unlocking, custom ROM installation.
- Exploit development, bypassing device protections, or covert monitoring.
- Claims that one command disables all Xiaomi telemetry.
- Claims that a normal cache clear or TRIM securely erases every NAND page.

## Safety principles

1. **Inspect before changing.** Capture the device model, build, package name, and current state.
2. **One package at a time.** Never run an unreviewed bulk-debloat script.
3. **Prefer reversible actions.** Where supported, `pm uninstall --user 0` removes an app for the current Android user; it is not the same as deleting the system image.
4. **Do not assume package names are universal.** Names and dependencies vary by model, region, Android version, and MIUI/HyperOS build.
5. **Do not disable core services casually.** Security Center, Security Core, power management, framework, account, and push services may have dependencies.
6. **Do not expose private evidence.** Redact serial numbers, account names, email addresses, phone numbers, Wi-Fi SSIDs, IP addresses, tokens, and notification contents before publishing.
7. **Do not promise secure deletion.** Flash storage wear-levelling and the flash translation layer prevent ordinary userland tools from proving physical erasure of every NAND page.

## Repository map

```text
Xiaomi-Privacy-Security-Research/
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
├── .gitignore
├── docs/
│   ├── 01-scope-and-methodology.md
│   ├── 02-setup-adb-windows.md
│   ├── 03-device-baseline-and-evidence.md
│   ├── 04-xiaomi-package-investigation.md
│   ├── 05-permissions-and-appops.md
│   ├── 06-analytics-diagnostics-and-privacy-settings.md
│   ├── 07-logs-network-and-evidence.md
│   ├── 08-safe-package-removal-and-rollback.md
│   ├── 09-cache-storage-trim-and-deleted-data.md
│   ├── 10-notifications-and-feedback-reporting.md
│   ├── 11-known-findings-redmi-k50i-case-study.md
│   ├── 12-troubleshooting.md
│   └── 13-glossary-and-references.md
├── scripts/
│   ├── xiaomi-readonly-audit.ps1
│   └── collect-package-details.ps1
└── templates/
    ├── finding-template.md
    └── device-profile-template.md
```

## Quick start

1. Read [Scope and methodology](docs/01-scope-and-methodology.md).
2. Install ADB using [the Windows setup guide](docs/02-setup-adb-windows.md).
3. Run the read-only audit script:
   ```powershell
   Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
   .\scripts\xiaomi-readonly-audit.ps1
   ```
4. Review the output and redact personal/device identifiers before sharing.
5. Use the investigation guides to understand a finding before making a change.

## Research status and transparency

The case study in this repository records a Redmi K50i / HyperOS investigation. It is a single-device observation, not a claim about every Xiaomi device. See [the case study](docs/11-known-findings-redmi-k50i-case-study.md).

Use the finding template for every new observation. Label it as:
- **Observed:** directly present in command output or settings.
- **Interpretation:** what the observation may mean.
- **Not established:** what the evidence does not prove.
- **Action:** what was changed, if anything.
- **Rollback:** how to restore the previous state, where possible.

## Disclaimer

Use these instructions only on a device you own or are authorized to administer. Commands can affect device functionality. The maintainers are not responsible for data loss, app failures, warranty issues, or device instability. Back up important data before making changes. Xiaomi may change package names, settings, and behavior across releases.

## Suggested portfolio description

**Xiaomi Privacy & Security Research Toolkit** — A practical Android research project documenting ADB-based inspection of Xiaomi/Redmi/POCO devices, including package inventory, permission and AppOps review, analytics and diagnostic settings, log/network evidence, safe user-level package management, and the limitations of secure deletion on flash storage. Emphasis is placed on reproducibility, evidence quality, safety, and clear explanations rather than blind debloating.
