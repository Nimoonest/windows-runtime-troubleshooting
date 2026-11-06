# Windows Runtime Playbook

Use the smallest applicable recovery and re-run a focused probe after it.

## Shell or sandbox helper fails before execution

**Symptoms**

- `windows sandbox: helper_unknown_error: setup refresh had errors`
- a command returns no process output because setup failed first;
- a file tool cannot read an existing path.

**Response**

1. Retry the exact narrow command once.
2. Request narrowly scoped elevated access if the operation is authorized and the failure is clearly sandbox setup.
3. For focused reads, try `rg -n` or a small file probe rather than a broad recursive command.
4. Do not interpret the error as a source conflict. Preserve the original files if the patch surface remains inaccessible.

## File edits and patch tools

When an edit helper cannot read a Windows file, do not switch to an unbounded text rewrite. Confirm the target, check whether a process holds it, retry narrowly, and use a byte-preserving tool or a new sibling artifact only when that is explicitly safe. Validate syntax and compare the resulting path before reporting success.

## Processes and ports

Probe before stopping anything:

```powershell
Get-NetTCPConnection -LocalPort 4178 -State Listen -ErrorAction SilentlyContinue
Get-CimInstance Win32_Process -Filter "Name = 'node.exe'" | Select-Object ProcessId,CommandLine
Invoke-WebRequest http://127.0.0.1:4178/ -UseBasicParsing
```

Reuse a clearly matching service. Otherwise start on an explicit free port and report it. Stop only a process that is in scope and identified with confidence.

## Browser runtime and automation

**Symptoms**

- `node_repl kernel exited unexpectedly`;
- browser setup reports `windows sandbox failed`;
- Playwright reports `Executable doesn't exist at ... ms-playwright ...`.

**Response**

Keep the required browser setup attempt and record its failure. If a local browser is installed, use the bundled Playwright module with that executable as a fallback, for example:

```js
chromium.launch({
  headless: true,
  executablePath: 'C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe'
})
```

Report this as local-browser verification, not verification through the unavailable connector or managed binary. Capture screenshots and DOM assertions when possible.

## Encoding, paths, and Chinese text

Treat three facts separately: file bytes, console rendering, and selector transport. If PowerShell displays mojibake, do not rewrite valid UTF-8 source just to make the console look right. Use UTF-8-safe tooling and stable ASCII selectors such as `data-testid`, `data-action`, or `data-route` for automation. For JavaScript file paths, use `fileURLToPath(new URL(...))` rather than URL pathname strings, especially under non-ASCII Windows profiles.

## WSL boundary

Check distribution visibility and execution context before assuming a Windows-side command reflects WSL state:

```powershell
wsl.exe -l -v
wsl.exe -d Ubuntu-22.04 -- bash -lc "pwd; command -v node; command -v python"
```

Prefer short single-purpose commands. Keep quoting simple across the PowerShell-to-WSL boundary and use the existing WSL installation when the task is scoped to it.

## Dependencies and missing executables

When a command is missing, identify which runtime is intended before installing anything. Probe the bundled or project-local executable, its version, and the module path. Do not install a global package merely to hide a PATH problem. For temporary validation dependencies, use an isolated directory and clean it afterward.

## UI interaction and verification fallbacks

If a click is intercepted, inspect the element that owns the pointer. A backdrop selector clicked at its center usually hits the dialog; click a visible close control or a coordinate outside the dialog. If screenshots or image helpers fail, keep DOM/layout assertions and report the visual inspection gap instead of claiming a screenshot review.

For responsive interfaces, check both the document width and visible control bounds at the target viewports. Hidden off-canvas navigation can create false positives; check the visible main content separately.
