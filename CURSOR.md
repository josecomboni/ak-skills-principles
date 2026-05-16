# Using this repo with Cursor

This project includes a **Cursor project rule** so the Karpathy-inspired layer-aware behavioral guidelines apply automatically when you work here.

## In this repository

1. Open the folder in Cursor.
2. The rule [`.cursor/rules/ak-skills-principles.mdc`](.cursor/rules/ak-skills-principles.mdc) is committed with `alwaysApply: true`, so you do not need extra installation steps.
3. In Cursor, you can confirm it under **Settings → Rules** (or the project rules UI), where `ak-skills-principles` should appear.

## Use the same guidelines in another project

**Cursor (recommended):** Copy `.cursor/rules/ak-skills-principles.mdc` into that project’s `.cursor/rules/` directory (create the folders if needed). Adjust or merge with existing rules as you like.

**Other tools:** If a stack only supports a root instruction file, copy [`CLAUDE.md`](CLAUDE.md) into that project instead (or merge its contents into your existing instructions).

## Optional: personal Agent Skills

If you want the task-driven skill as a reusable skill under `~/.cursor/skills`, use [`skills/ak-skills-principles/SKILL.md`](skills/ak-skills-principles/SKILL.md). Keep [`orchestrators/ak-skills-principles/ORCHESTRATOR.md`](orchestrators/ak-skills-principles/ORCHESTRATOR.md) and [`supervisors/ak-skills-principles/SUPERVISOR.md`](supervisors/ak-skills-principles/SUPERVISOR.md) as separate references for planning and oversight agents.

## Claude Code vs Cursor

- **Claude Code:** Install via the plugin marketplace and [`README.md`](README.md) instructions; the plugin exposes the task-driven skill from this repo. Per-project use can also rely on `CLAUDE.md`.
- **Cursor:** Use the committed `.cursor/rules/` file as described above. Cursor does not read `.claude-plugin/` or `CLAUDE.md` by default.

## For contributors

When you change layer-wide guidance, keep **[`CLAUDE.md`](CLAUDE.md)** and **[`.cursor/rules/ak-skills-principles.mdc`](.cursor/rules/ak-skills-principles.mdc)** in sync. When you change a specific layer, update its source file too: task guidance in **[`skills/ak-skills-principles/SKILL.md`](skills/ak-skills-principles/SKILL.md)**, orchestration guidance in **[`orchestrators/ak-skills-principles/ORCHESTRATOR.md`](orchestrators/ak-skills-principles/ORCHESTRATOR.md)**, and supervision guidance in **[`supervisors/ak-skills-principles/SUPERVISOR.md`](supervisors/ak-skills-principles/SUPERVISOR.md)**.
