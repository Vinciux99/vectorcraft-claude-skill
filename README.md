# VectorCraft skill for Claude

VectorCraft (ArtCraft suite, storytold/vectorcraft; formerly DrawCraft) is an Illustrator-style vector editor: paths, shapes, text, multi-fill/stroke appearance, live effects, symbols, swatches, artboards, Image Trace, graphs, perspective, export to SVG/PDF/AI/EPS/PNG and more. Everything is a command with an id and JSON parameters, reachable from the UI, CLI, control channel and MCP.

This repository packages a **skill** that teaches Claude to drive VectorCraft (part of the [ArtCraft suite](https://github.com/storytold/vectorcraft) by storytold) and that **knows how to create the MCP server and guide you through it**, on **Windows and macOS**. It is unofficial and not affiliated with storytold.

> Part of the [ArtCraft Claude skills hub](https://github.com/Vinciux99/artcraft-claude-skills): all 13 skills, a picker and a one-command installer.

## What it does
- Operating manual for VectorCraft: connection modes, ground rules, every MCP tool, recipes, CLI, control channel, command families and a safe workflow (work on copies, verify, save only with your OK).
- Built-in MCP setup assistant: Claude checks whether the server is connected, then walks you through installing it (automatic script or manual config), the launcher, authentication and verification.
- Troubleshooting table for the usual problems (server "disconnected", ports, macOS quarantine, Windows Store config location...).

## What you can ask Claude
- "Draw a 5-point star, gold fill, black 2 pt outline, centred on the artboard, and export it as SVG."
- "Create an icon set of 6 simple weather icons on a 24 pt grid and export each as a separate SVG."
- "Trace this PNG logo with Image Trace and give me a clean SVG."
- "Make a poster 18×24 in with this headline, this palette and a bleed, and export a print PDF."
- "Recolor the selection with a split-complementary theme based on #2266aa."
- "Build a bar chart from this CSV and style it with my brand colors."
- "Export every artboard as PNG at 2×."
- "Check whether this Illustrator .jsx script will run in VectorCraft."

## Highlights
- Three connection modes (live, headless, automatic) with read/write roots for safe file access.
- Dedicated drawing tools plus 100s of commands by family, with `$N` references between batch steps.
- Detailed export options for SVG, PDF/AI, raster, per-artboard output and CMYK documents.
- CLI `run` pipelines and the unauthenticated control channel documented end to end.
- Built-in guidance for creating and troubleshooting the MCP server on Windows and macOS.

## App-specific notes
- Kit port **7979** (same as upstream docs); flag is `--connect`; headless `mcp --headless`.
- The control channel has no authentication, loopback only.
- Use `--automation-read-root/--automation-write-root` to confine file access.

## Get the skill

| Where | How |
|---|---|
| **Claude app** (claude.ai / Desktop) | Download [`vectorcraft-skill.zip](https://github.com/Vinciux99/vectorcraft-claude-skill/releases/latest/download/vectorcraft-skill.zip)`, then Customize > Skills > "+" > Create skill > Upload a skill. Needs Settings > Capabilities > "Code execution and file creation" on. |
| **Claude Code, plugin** | `/plugin marketplace add Vinciux99/artcraft-claude-skills` then `/plugin install vectorcraft@artcraft` |
| **Claude Code, manual** | copy `skills/vectorcraft/` to `~/.claude/skills/vectorcraft/` (the setup script below does it) |

## Create the MCP server

Prerequisite: install VectorCraft from [upstream releases](https://github.com/storytold/vectorcraft/releases) (Windows `.msi`, macOS `.dmg`) and Claude Desktop and/or Claude Code.

**One command (recommended)**

Windows (PowerShell):
```powershell
powershell -NoProfile -ExecutionPolicy Bypass -Command "& ([scriptblock]::Create((irm https://raw.githubusercontent.com/Vinciux99/artcraft-claude-skills/main/install.ps1))) -Apps vectorcraft"
```
macOS (Terminal):
```sh
curl -fsSL https://raw.githubusercontent.com/Vinciux99/artcraft-claude-skills/main/install.sh | bash -s -- --apps vectorcraft
```
Add `-DryRun` (Windows) or `--dry-run` (macOS) to preview, and `-Mode live|headless|both` / `--mode ...` to choose the mode.

**Or from a clone:** double-click `setup-windows.cmd` (Windows) or `setup-macos.command` (macOS; first time right-click > Open).

**Or just ask Claude:** once the skill is installed, say "set up the VectorCraft MCP server" and Claude will guide you step by step.

After setup: fully quit and reopen Claude, open VectorCraft with the **"VectorCraft (Claude)"** launcher, then ask Claude to use it. If the server shows "disconnected", the app was not running when Claude started: open it with the launcher and re-enable the server, or use headless mode.

What the setup changes (all backed up or reversible): Claude Desktop config (`mcpServers.vectorcraft`, backup `<file>.backup-<timestamp>`), the launcher, Claude Code's user-scope MCP list and `~/.claude/skills/vectorcraft/`.

## Repository layout
```
skills/vectorcraft/SKILL.md      the skill
app.json                   machine-readable settings (ports, paths, MCP args) used by the scripts
scripts/                   artcraft-setup.ps1 (Windows), artcraft-setup.sh (macOS), json-tool.js
setup-windows.cmd          double-click wrapper
setup-macos.command        double-click wrapper
.claude-plugin/plugin.json Claude Code plugin manifest
.github/workflows/ci.yml   validation, setup tests on Windows and macOS, release zip
```

## CI and releases
Every push validates `app.json`, `SKILL.md` and the scripts, then runs the setup scripts against a fake install on **Windows** and **macOS** runners. Every push to `main` builds `vectorcraft-skill.zip` and publishes (or updates) the GitHub release `v1.0.0`.

## Status and caveats
- Windows paths and arguments come from a working install. The macOS paths (`/Applications/VectorCraft.app`, CLI location and release asset name) follow the upstream packaging but have **not** been verified on a Mac; the skill marks unverified items and the setup script reports what it could not find.
- VectorCraft is a young open-source project; behaviour can change between releases. `list_commands` / `vectorcraft-cli commands` is always the source of truth.
- Unofficial community packaging, MIT licensed (see `LICENSE`).
