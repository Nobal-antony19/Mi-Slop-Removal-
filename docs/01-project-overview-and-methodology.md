# Project Overview & Research Methodology

## 1. Introduction

Mi Slop Removal is an independent Xiaomi-focused
Android privacy and security research project.

The project explores how Xiaomi, Redmi and POCO
devices running MIUI or HyperOS manage system
applications, background services, permissions,
diagnostic components and device-level privacy
settings.

Using Android Debug Bridge (ADB), Windows
PowerShell and structured investigation techniques,
the project aims to help users understand what
exists on their devices, what individual components
do, and what can be safely configured.

This is not a blind debloating list.

The objective is to investigate before modifying.

## 2. Project Objectives

- Identify preinstalled Xiaomi system packages.
- Understand package permissions and capabilities.
- Investigate analytics and diagnostic components.
- Examine Android services, providers and receivers.
- Review privacy and authorization settings.
- Inspect available logs and network accounting.
- Identify unnecessary applications and features.
- Document safe package management procedures.
- Explain risks, limitations and recovery methods.
- Build reproducible, evidence-based device reports.

## 3. Supported Device Ecosystem

This project focuses on:

- Xiaomi smartphones
- Redmi smartphones
- POCO smartphones
- MIUI-based firmware
- HyperOS-based firmware

Package names, system architecture and available
commands may differ between models, regions,
Android releases and firmware versions.

Never assume that a package verified on one device
is safe to remove from another.

## 4. Research Environment

Primary environment:

| Component | Platform |
|---|---|
| Host OS | Windows 10 / Windows 11 |
| Device OS | MIUI / HyperOS |
| Interface | Android Debug Bridge |
| Shell | Windows PowerShell |
| Device access | USB debugging |
| Analysis | Package metadata, AppOps and logs |

Root access and bootloader unlocking are not
required for the standard inspection workflow.

## 5. Research Methodology

Every investigation should follow this process.

### Phase 1 — Device Identification

Record:

- Manufacturer and model
- Device codename
- Android version
- MIUI / HyperOS version
- Firmware build
- Security patch level

### Phase 2 — Baseline Inspection

Collect the initial device state.

Examples:

- Installed package inventory
- Third-party applications
- Xiaomi system packages
- Available storage
- Relevant system properties

### Phase 3 — Component Investigation

Inspect individual packages and their:

- APK locations
- Requested permissions
- Granted permissions
- Services
- Content providers
- Broadcast receivers
- AppOps configuration

### Phase 4 — Privacy Analysis

Investigate:

- Analytics-related components
- Diagnostic and feedback services
- Authorization settings
- Usage and diagnostics controls
- Personalized advertising settings
- Available network accounting

### Phase 5 — Risk Assessment

Classify each finding based on:

- Observed functionality
- Privacy implications
- System dependencies
- Potential side effects
- Confidence in available evidence

### Phase 6 — Controlled Changes

Where justified, make one change at a time.

Document:

- Why the change was necessary
- Exact command or settings path
- Expected result
- Actual result
- Possible side effects
- Recovery procedure

### Phase 7 — Verification

Reinspect the device and compare the
result against the original baseline.

Do not consider a change successful until
its result has been verified.

## 6. Evidence Classification

All research findings should use these labels.

| Classification | Meaning |
|---|---|
| Observed | Directly confirmed by device output |
| Corroborated | Supported by multiple observations |
| Interpretation | A reasonable explanation of evidence |
| Unverified | Requires further investigation |
| Not established | Current evidence cannot prove the claim |
| Verified action | A modification was tested and checked |

For example:

An application requesting Internet permission
does not prove that it transmitted telemetry.

Network byte counters do not reveal the actual
contents of transmitted data.

A declared system service does not prove that
the service is actively running.

## 7. Safety Principles

- Inspect before modifying.
- Investigate one package at a time.
- Avoid blindly copying debloat commands.
- Do not remove critical Android or Xiaomi services.
- Maintain a recovery plan.
- Back up important information.
- Redact private information from research output.
- Do not run destructive commands without understanding them.

## 8. Important Limitations

This project does not guarantee:

- Complete elimination of Xiaomi telemetry.
- Detection of every malicious application.
- Prevention of all background network activity.
- Forensic unrecoverability of deleted files.
- Identical behaviour across all Xiaomi devices.

Flash storage uses wear-levelling, garbage
collection and logical-to-physical address mapping.

Ordinary file deletion, cache clearing and
TRIM operations cannot independently prove
physical erasure of every NAND storage page.

## 9. Project Philosophy

The purpose of this project is not to remove
everything that looks unnecessary.

It is to understand the device, collect evidence,
evaluate risks and make informed decisions.

**Inspect first. Understand everything.
Remove only what you can justify.**
