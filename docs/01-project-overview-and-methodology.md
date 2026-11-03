# 01 — Project Overview & Research Methodology

## Introduction
**Mi Slop Removal** is an independent Xiaomi-focused Android privacy and security research project. It explores system applications, background services, permissions, diagnostics and device-level privacy settings on Xiaomi, Redmi and POCO devices running MIUI or HyperOS.

This is not a blind debloating list. The objective is to investigate before modifying.

## Objectives
- Identify preinstalled Xiaomi packages.
- Understand package permissions and declared capabilities.
- Investigate analytics and diagnostic components.
- Examine services, providers and receivers.
- Review privacy and authorization settings.
- Inspect available logs and network accounting.
- Document safe package-management and recovery procedures.
- Build reproducible, evidence-based device reports.

## Scope and environment
| Area | Scope |
|---|---|
| Devices | Xiaomi, Redmi, POCO |
| Software | MIUI / HyperOS |
| Host | Windows 10 / 11 |
| Interface | Android Debug Bridge (ADB) |
| Shell | PowerShell |
| Access | Standard ADB; no root required |

Package names and dependencies vary by model, region, Android release and firmware build. A result on one device is not a universal recommendation.

## Methodology
1. **Identify:** Record model, codename, Android version, firmware build and security patch.
2. **Baseline:** Inventory packages, third-party apps and storage.
3. **Investigate:** Inspect APK paths, permissions, services, providers, receivers and AppOps.
4. **Analyse:** Review privacy settings, diagnostics, logs and available network accounting.
5. **Assess:** Evaluate evidence, dependencies, privacy implications and side effects.
6. **Change:** Make one justified change at a time, preferably reversible.
7. **Verify:** Reinspect and compare against the original baseline.

## Evidence labels
| Label | Meaning |
|---|---|
| Observed | Directly confirmed by device output or UI |
| Corroborated | Supported by multiple observations |
| Interpretation | Reasonable explanation, not direct proof |
| Unverified | Requires further investigation |
| Not established | Evidence cannot prove the claim |
| Verified action | A change was made and checked |

Internet permission does not prove data transmission. Network byte counters do not reveal payload contents. A declared service does not prove it is running.

## Safety
Inspect before modifying. Investigate one package at a time. Do not blindly remove core Android or Xiaomi services. Back up important data, maintain a recovery plan and redact private information from published evidence.

## Limitations
This project does not guarantee complete elimination of telemetry, detection of every malicious application, prevention of all background traffic or forensic unrecoverability of deleted files. Flash storage wear-levelling and logical-to-physical address mapping prevent ordinary userland commands from proving erasure of every NAND page.

**Inspect first. Understand everything. Remove only what you can justify.**
