---
name: career-ops
description: AI job search command center for Pi. Use when the user asks to evaluate jobs, generate tailored CVs, scan portals, track applications, batch-process roles, prepare outreach, or invokes /career-ops commands.
---

# career-ops -- Pi Router

This project is designed to run in [Pi](https://github.com/badlogic/pi-coding-agent). Pi loads `AGENTS.md` as project context and exposes `.pi/prompts/*.md` files as slash commands.

## Mode Routing

Determine the mode from the user's arguments after `/skill:career-ops`, `/career-ops`, or one of the `.pi/prompts/` command templates.

| Input | Mode |
|-------|------|
| empty / no args | `discovery` -- Show command menu |
| JD text or URL with no sub-command | **`auto-pipeline`** |
| `oferta`, `evaluate` | `oferta` |
| `ofertas`, `compare` | `ofertas` |
| `contacto`, `contact` | `contacto` |
| `deep` | `deep` |
| `pdf` | `pdf` |
| `training` | `training` |
| `project` | `project` |
| `tracker` | `tracker` |
| `pipeline` | `pipeline` |
| `apply` | `apply` |
| `scan` | `scan` |
| `batch` | `batch` |
| `patterns` | `patterns` |
| `followup` | `followup` |

**Auto-pipeline detection:** If the arguments are not a known sub-command and contain JD text (keywords: "responsibilities", "requirements", "qualifications", "about the role", "we're looking for", company name + role) or a URL to a JD, execute `auto-pipeline`.

If the arguments are not a sub-command and do not look like a JD, show discovery.

---

## Discovery Mode (no arguments)

Show this menu:

```text
career-ops -- Command Center for Pi

Available commands:
  /career-ops {JD or URL}      → AUTO-PIPELINE: evaluate + report + PDF + tracker
  /career-ops pipeline         → Process pending URLs from data/pipeline.md
  /career-ops oferta           → Evaluation only A-G (no auto PDF)
  /career-ops ofertas          → Compare and rank multiple offers
  /career-ops contacto         → LinkedIn outreach: find contacts + draft message
  /career-ops deep             → Deep company research
  /career-ops pdf              → PDF only, ATS-optimized CV
  /career-ops training         → Evaluate course/cert against goals
  /career-ops project          → Evaluate portfolio project idea
  /career-ops tracker          → Application status overview
  /career-ops apply            → Live application assistant
  /career-ops scan             → Scan portals and discover new offers
  /career-ops batch            → Batch processing with parallel pi workers
  /career-ops patterns         → Analyze rejection patterns and improve targeting
  /career-ops followup         → Follow-up cadence tracker and draft generator

Convenience aliases are also available, e.g. /career-ops-scan and /career-ops-pdf.
Inbox: add URLs to data/pipeline.md → /career-ops pipeline
Or paste a JD directly to run the full pipeline.
```

---

## Context Loading by Mode

After determining the mode, load the necessary files before executing:

### Modes that require `_shared.md` + their mode file
Read `modes/_shared.md` + `modes/{mode}.md`.

Applies to: `auto-pipeline`, `oferta`, `ofertas`, `pdf`, `contacto`, `apply`, `pipeline`, `scan`, `batch`.

### Standalone modes
Read only `modes/{mode}.md`.

Applies to: `tracker`, `deep`, `training`, `project`, `patterns`, `followup`.

### Language-specific modes
If `config/profile.yml` has `language.modes_dir` set, or the user asks for German/French/Japanese/Portuguese/Russian modes, load files from that directory when present and fall back to `modes/` for missing modes.

## Pi Execution Notes

- Use Pi's built-in tools (`read`, `bash`, `edit`, `write`) to inspect and modify files.
- Pi prompt templates live in `.pi/prompts/` and are invoked as `/career-ops...` commands.
- For batch processing, use `batch/batch-runner.sh`; it launches isolated `pi -p` workers.
- For long scans or multi-URL pipeline runs, use a subagent extension only if one is available in the active Pi environment. Otherwise execute the workflow directly in the current session.
- Never submit an application on the user's behalf. Stop before final Submit/Send/Apply.

Execute the instructions from the loaded mode file.
