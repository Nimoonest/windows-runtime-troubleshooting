---
name: windows-runtime-troubleshooting
description: Diagnose and recover Windows runtime and local tooling failures across development, automation, WSL, file operations, encoding, processes, ports, and verification. Use when commands fail before execution, paths or Chinese text behave incorrectly, browser/runtime binaries are missing, or a local service cannot be inspected or started.
---

# Windows Runtime Troubleshooting

Use this skill for Windows environment failures that can be confused with application bugs. Keep every recovery scoped, preserve user data, and separate direct evidence from fallback verification.

## Workflow

1. Classify the failure: shell/helper, file operation, process/port, browser/runtime, encoding/path, WSL, dependency, or verification.
2. Capture the literal error, command, target path, working directory, and process/URL involved.
3. Retry one narrow operation once. Do not repeat a known environment failure indefinitely.
4. Read the matching section in [references/windows-playbook.md](references/windows-playbook.md).
5. Apply the smallest reversible recovery, then run a focused probe.
6. Report what was directly verified, what used a fallback, and what remains unverified.

## Guardrails

- Treat `helper_unknown_error`, permission-helper failures, and browser-kernel exits as environment failures until source-level evidence says otherwise.
- Do not turn a failed patch into broad PowerShell rewriting, recursive deletion, or an overwrite of an unknown target.
- Resolve exact paths before moving or deleting anything. Preserve user changes and secrets.
- Probe process ownership and ports before stopping a service or choosing a replacement port.
- Preserve UTF-8 source bytes. Console mojibake is not proof that the file is corrupted.
- Do not claim end-to-end verification when the requested runtime or browser surface was unavailable; name the fallback explicitly.

## Evidence standard

For a local service, check the URL and response content. For an application, run its focused check. For browser work, test the primary user action and the relevant viewport or state. For WSL or a dependency, probe the exact distribution, executable, or module rather than inferring from a generic PATH check.

Read only the relevant section of the playbook for the current symptom. Keep the main workflow stable and put machine-specific commands in the reference.
