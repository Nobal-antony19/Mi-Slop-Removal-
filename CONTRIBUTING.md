# Contributing

Contributions should improve accuracy, safety, clarity, or reproducibility for Xiaomi/Redmi/POCO devices.

## Before submitting
- State the exact device model, region if known, Android version, MIUI/HyperOS version, and security patch date.
- Include the exact command and sanitized output.
- Explain whether the command is read-only or modifies the device.
- Explain potential side effects and rollback steps.
- Do not publish personal identifiers, tokens, private logs, or account information.
- Do not submit universal "safe to remove" claims without model/build-specific evidence.

## Finding format
Use `templates/finding-template.md`. Distinguish observation from interpretation and explicitly state what the evidence does not establish.

## Package recommendations
A package should not be recommended for removal merely because its name looks like analytics, feedback, ads, or tracking. Include package metadata, permissions, service/provider information where relevant, dependency considerations, tested device/build, and a recovery path.
