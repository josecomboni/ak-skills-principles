# AK Skills Principles

> Check out my new project [Multica](https://github.com/multica-ai/multica) — an open-source platform for running and managing coding agents with reusable skills.
>
> Follow me on X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

Layer-aware guidelines for AI coding agents, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

English | [简体中文](./README.zh.md)

## The Problems

From Andrej's post:

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

> "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

> "They still sometimes change/remove comments and code they don't sufficiently understand as side effects, even if orthogonal to the task."

## The Layered Solution

The key distinction:

| Layer | Driven by | Actor | Responsibility |
|-------|-----------|-------|----------------|
| **Skill** | **Task** | Executor model | Execute one bounded operation with a clear contract |
| **Orchestrator** | **Goal** | Planning/coordinating model | Decompose goals into tasks, dispatch skills, and verify outcomes |
| **Supervisor** | **Oversight** | Separate AI/model set by default; HITL only by override | Review plans, diffs, verification evidence, and recurring lessons |

Skills should not behave like autonomous planners. Orchestrators should not bury goal decomposition inside a skill. Supervisors should not be the same unchecked executor grading its own work.

## Repository Layout

| Path | Purpose |
|------|---------|
| [`skills/ak-skills-principles/SKILL.md`](skills/ak-skills-principles/SKILL.md) | Task-driven skill guidelines |
| [`orchestrators/ak-skills-principles/ORCHESTRATOR.md`](orchestrators/ak-skills-principles/ORCHESTRATOR.md) | Goal-driven orchestration guidelines |
| [`supervisors/ak-skills-principles/SUPERVISOR.md`](supervisors/ak-skills-principles/SUPERVISOR.md) | Oversight and continuous-improvement guidelines |
| [`CLAUDE.md`](CLAUDE.md) | Layer-aware root instruction file |
| [`.cursor/rules/ak-skills-principles.mdc`](.cursor/rules/ak-skills-principles.mdc) | Cursor project rule |
| [`EXAMPLES.md`](EXAMPLES.md) | Concrete examples and anti-patterns |

## Skill Layer: Task-Driven Execution

**Execute the assigned task contract. Verify it. Stop.**

Task skills use four principles:

| Principle | Addresses |
|-----------|-----------|
| **Think Before Acting** | Wrong assumptions, hidden confusion, missing tradeoffs |
| **Simplicity First** | Overcomplication, bloated abstractions |
| **Surgical Changes** | Orthogonal edits, touching code you should not touch |
| **Task-Driven Execution** | Scope drift, vague outputs, unverified task completion |

When acting as a skill:

- Identify the task input, expected output, constraints, and done condition.
- Do not expand a task into a broader product goal or adjacent cleanup.
- If the task lacks required information, ask for clarification or report a blocker.
- Use the smallest verification that proves the task contract is satisfied.
- Report exactly what changed, what was verified, and any explicit blockers; then stop.

## Orchestrator Layer: Goal-Driven Execution

**Define the outcome, decompose into tasks, dispatch, and verify.**

Orchestrators own the Karpathy insight:

> "LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."

When acting as an orchestrator:

- Convert user goals into explicit success criteria and acceptance checks.
- Break the goal into bounded tasks that skills can execute without scope drift.
- Track dependencies and sequence work so each task has a clear contract.
- Verify task outputs and the final goal outcome; do not confuse task completion with goal completion.
- For non-trivial work, write a brief plan before dispatching tasks.

## Supervisor Layer: Oversight and Continuous Improvement

**Review independently. Learn from every run. Keep skills lean.**

The supervisor replaces "watch like a hawk" with a repeatable oversight loop:

- Prefer a separate AI model or model set from the executor/orchestrator to reduce shared blind spots.
- Use HITL only when explicitly requested by the user or required by a user-configured policy.
- Review plans, task decomposition, diffs, test results, final claims, and signs of scope drift.
- Promote repeated, durable corrections into skill updates.
- Prune stale, redundant, or low-value guidance so skills stay small and sharp.
- Version and diff skill changes like code changes.

For humans, the original atrophy warning still applies: occasionally write and review code manually so your discrimination ability does not decay. For AI supervisors, anti-atrophy means self-learning without instruction bloat.

## Install

**Option A: Claude Code Plugin (recommended)**

From within Claude Code, first add the marketplace:

```text
/plugin marketplace add josecomboni/ak-skills-principles
```

Then install the plugin:

```text
/plugin install ak-skills-principles@ak-skills-principles
```

This installs the task-driven skill from this repo.

**Option B: CLAUDE.md (per-project)**

New project:

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/josecomboni/ak-skills-principles/main/CLAUDE.md
```

Existing project (append):

```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/josecomboni/ak-skills-principles/main/CLAUDE.md >> CLAUDE.md
```

## Using with Cursor

This repository includes a committed Cursor project rule ([`.cursor/rules/ak-skills-principles.mdc`](.cursor/rules/ak-skills-principles.mdc)) so the same layer-aware guidelines apply when you open the project in Cursor. See **[CURSOR.md](CURSOR.md)** for setup, using the rule in other projects, and how this relates to Claude Code.

## How to Know It's Working

These guidelines are working if you see:

- **Skills produce narrow task outputs** — no drive-by refactoring or invented scope.
- **Orchestrators reach verified goals** — plans, tasks, and final checks trace back to acceptance criteria.
- **Supervisors catch drift before merge** — review is independent and evidence-based.
- **Skills improve without bloating** — recurring lessons are promoted, duplicate or stale rules are pruned.

## Customization

These guidelines are designed to be merged with project-specific instructions. Add them to your existing `CLAUDE.md` or create a new one.

For project-specific rules, add sections like:

```markdown
## Project-Specific Guidelines

- Use TypeScript strict mode
- All API endpoints must have tests
- Follow the existing error handling patterns in `src/utils/errors.ts`
```

## Tradeoff Note

These guidelines bias toward **caution over speed**. For trivial tasks (simple typo fixes, obvious one-liners), use judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on non-trivial work, not slowing down simple tasks.

## License

MIT
