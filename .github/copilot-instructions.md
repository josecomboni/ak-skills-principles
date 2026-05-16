# andrej-karpathy-skills — Copilot Instructions

## What this repo is

A documentation-only repo that distributes Karpathy-inspired behavioral guidelines for three agent layers: task-driven skills, goal-driven orchestrators, and oversight-driven supervisors. There is no source code, no build, no tests.

## Distribution surfaces (keep in sync)

The guidelines are duplicated or summarized across several files. When a layer changes, update every surface that describes that layer:

| File | Consumer | Notes |
|------|----------|-------|
| `CLAUDE.md` | Claude Code (per-project) | Layer-aware root instruction file |
| `.cursor/rules/karpathy-guidelines.mdc` | Cursor | Layer-aware rule with `alwaysApply: true` frontmatter |
| `skills/karpathy-guidelines/SKILL.md` | Claude plugin skill | Task-driven skill only; frontmatter has `name`, `description`, `license` |
| `orchestrators/karpathy-guidelines/ORCHESTRATOR.md` | Orchestrator agents | Goal-driven decomposition and verification |
| `supervisors/karpathy-guidelines/SUPERVISOR.md` | Supervisor agents | AI-first oversight, HITL override, and lean skill improvement |
| `README.md` | GitHub readers | Long-form overview |
| `EXAMPLES.md` | GitHub readers | Concrete examples |

`CURSOR.md` documents the Cursor-specific workflow and references the rule path — update it only if file paths or install steps change.

`README.zh.md` is the Chinese translation of `README.md` and must be kept in sync when the principles or install instructions change.

## Plugin metadata

- `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` declare the Claude Code plugin. Bump `version` in both when releasing.
- The plugin name (`andrej-karpathy-skills`) and marketplace id (`karpathy-skills`) are referenced in `README.md` install commands — keep them aligned.

## Content conventions

- Keep the layer split explicit: **skills are task-driven**, **orchestrators are goal-driven**, and **supervisors are oversight-driven**.
- Do not put goal discovery or task decomposition back into `SKILL.md`; that belongs in `ORCHESTRATOR.md`.
- Supervisor guidance should default to separate AI/model-set oversight, with HITL only by explicit user override or user-configured policy.
- Skill updates must stay lean: promote repeated durable lessons, merge duplicates, and prune stale guidance.
- Each principle file opens with a tradeoff note appropriate to its layer.
- `EXAMPLES.md` uses Python for all code samples; keep the language consistent if adding examples.

## License

MIT — declared in `CLAUDE.md` frontmatter (via skill), `plugin.json`, and `LICENSE` should it be added.
