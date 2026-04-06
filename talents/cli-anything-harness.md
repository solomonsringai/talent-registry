# Talent: CLI-Anything — Agent-Controlled Desktop Software

> Wrap any desktop application in a CLI that agents can drive. Point an agent at Blender, GIMP, Audacity, OrcaSlicer — it controls the app through structured commands with JSON output.

## What It Is

CLI-Anything (HKUDS, University of Hong Kong) generates Click-based Python CLIs that wrap desktop software. Each harness:
- Exposes the app's features as CLI commands
- Returns structured JSON output (no screen scraping)
- Has session management (undo/redo, state persistence)
- Ships with a SKILL.md for agent auto-discovery
- Works with Claude Code, OpenClaw, Cursor, Codex, any agent platform

## Repo Location

```
~/CLI-Anything/
```

## Available Harnesses

| Harness | App | Install Dir | Status |
|---------|-----|-------------|--------|
| blender | Blender (3D modeling, rendering) | `blender/agent-harness/` | NOT INSTALLED |
| gimp | GIMP (image editing) | `gimp/agent-harness/` | NOT INSTALLED |
| audacity | Audacity (audio editing, uses sox) | `audacity/agent-harness/` | NOT INSTALLED |
| inkscape | Inkscape (vector graphics) | `inkscape/agent-harness/` | NOT INSTALLED |
| kdenlive | Kdenlive (video editing) | `kdenlive/agent-harness/` | NOT INSTALLED |
| shotcut | Shotcut (video editing) | `shotcut/agent-harness/` | NOT INSTALLED |
| libreoffice | LibreOffice (docs, sheets) | `libreoffice/agent-harness/` | NOT INSTALLED |
| obs-studio | OBS Studio (streaming/recording) | `obs-studio/agent-harness/` | NOT INSTALLED |
| comfyui | ComfyUI (AI image gen) | `comfyui/agent-harness/` | NOT INSTALLED |
| drawio | Draw.io (diagrams) | `drawio/agent-harness/` | NOT INSTALLED |
| mermaid | Mermaid (diagram code) | `mermaid/agent-harness/` | NOT INSTALLED |
| zoom | Zoom (conferencing) | `zoom/agent-harness/` | NOT INSTALLED |
| anygen | AnyGen (AI content) | `anygen/agent-harness/` | NOT INSTALLED |

## How to Install a Harness

```bash
cd ~/CLI-Anything/<app>/agent-harness/
pip install -e .
```

After install, the CLI is available as a Python module:

```bash
# Example: Blender harness
python3 -m cli_anything.blender.blender_cli scene new --name "MyScene"
python3 -m cli_anything.blender.blender_cli object add cube --name "MyCube"
python3 -m cli_anything.blender.blender_cli render run --output /path/to/render.png
```

## Blender Harness — Command Groups

The Blender harness (our highest priority) exposes:

| Group | Commands | What |
|-------|----------|------|
| `scene` | new, load, save, info | Scene management |
| `object` | add, delete, move, rotate, scale, list | Object manipulation |
| `material` | create, assign, list | PBR material setup |
| `modifier` | add, remove, list | Boolean, subdivision, etc. |
| `lighting` | add, remove, configure | Light types and settings |
| `animation` | keyframe, timeline | Keyframe animation |
| `render` | run, configure | Cycles/EEVEE rendering |
| `repl` | (interactive mode) | Stateful REPL session |

All commands accept `--json` flag for structured output.

## OpenClaw Integration

Deploy the SKILL.md so Simon and other OpenClaw agents can discover it:

```bash
cp ~/CLI-Anything/openclaw-skill/SKILL.md ~/.openclaw/skills/cli-anything.md
```

## Claude Code Integration

The harnesses work directly from Claude Code — just install and call via Bash tool:

```bash
python3 -m cli_anything.blender.blender_cli --json scene new --name "ProductShot"
python3 -m cli_anything.blender.blender_cli --json object add cube --name "Case" --size 0.049,0.1147,0.018
python3 -m cli_anything.blender.blender_cli --json render run --engine cycles --output ~/Downloads/render.png
```

## Priority Install Order for The Shop

1. **Blender** — 3D modeling, The Construct, product renders (we're already using bpy headless)
2. **Audacity** — audio editing for voice pipeline, narration cleanup
3. **GIMP** — thumbnail generation, image manipulation
4. **OBS Studio** — screen recording for tutorials/demos
5. **Kdenlive** — video editing if DaVinci Resolve isn't installed

## When to Use This vs Raw Commands

| Situation | Use |
|-----------|-----|
| One-off Blender render | Raw `bpy` Python script (what we do now) |
| Repeated/complex scene builds | CLI-Anything Blender harness — session state, undo, JSON output |
| Agent needs to edit images | GIMP harness — structured commands beat raw Script-Fu |
| Agent needs audio cleanup | Audacity harness — sox-backed, clean API |
| Building a custom harness | CLI-Anything's generator pipeline — point it at app source, get a CLI |

## Custom Harness Generation

CLI-Anything can generate NEW harnesses for apps that don't have one yet:

```bash
# The generation pipeline (6 phases):
# 1. Source code analysis
# 2. Feature extraction
# 3. CLI architecture design
# 4. Code generation (Click-based)
# 5. Test generation
# 6. SKILL.md generation

# Candidates for The Shop:
# - OrcaSlicer (3D print slicing)
# - ComfyUI (already has harness, needs configuration)
# - Home Assistant (IoT control)
```

## Gotchas

- Harnesses are NOT pip-installed — they exist in the repo but are not on PATH until `pip install -e .`
- The Blender egg-info exists but the CLI is NOT functional without install
- Some harnesses need the target app installed (GIMP, Inkscape, Audacity, OBS)
- The mesh_env2 venv at `/private/tmp/claude-501/` is temporary — may not persist across reboots
- JSON output mode (`--json`) is essential for agent consumption — always use it

---

*Documented: 2026-03-19 by Cameron (Claude Code)*
*Repo: https://github.com/HKUDS/CLI-Anything (MIT license)*
*Local clone: ~/CLI-Anything/ (cloned 2026-03-12)*
