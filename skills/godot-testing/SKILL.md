---
name: godot-testing
description: Validate Godot 4 scene and script changes by running the local Godot console executable headlessly with a sandbox-safe project-local log file. Use when editing `.tscn`, `.gd`, or `project.godot` files, or when a Godot scene needs verification before handoff.
---

# Godot Testing

## Workflow

1. From the Godot project root, locate the newest installed Godot console executable under `C:\Program Files\Godot`:

   ```powershell
   $godot = Get-ChildItem -Path "$env:ProgramFiles\Godot" -Recurse -Filter "*console*.exe" -ErrorAction SilentlyContinue |
       Sort-Object LastWriteTime -Descending |
       Select-Object -First 1 -ExpandProperty FullName

   if (-not $godot) {
       throw "Godot console executable not found under $env:ProgramFiles\Godot. Update the godot-testing skill with the installed Godot console path."
   }
   ```

2. Run the project headlessly, using a project-relative log file so sandboxed runs do not crash while writing to `user://logs`:

   ```powershell
   & $godot --headless --path . --quit --log-file sandbox-godot.log
   ```

3. Read the console output and `sandbox-godot.log` for script parse errors, load errors, and other startup failures.

4. If the scene fails, fix the issue and rerun the same command before handing the change back.

5. Leave `sandbox-godot.log` in place when it is ignored by git.
