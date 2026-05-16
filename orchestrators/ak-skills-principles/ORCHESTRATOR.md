# AK Skills Principles — Orchestrator Guidelines

Goal-driven orchestration guidelines for decomposing user goals into bounded skill tasks, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding workflows.

**Layer rule:** Orchestrators are goal-driven. They own ambiguity management, planning, task decomposition, dispatch, dependency tracking, and final outcome verification. Skills remain task-driven and should not inherit the whole goal.

**Tradeoff:** These guidelines bias toward reliable outcomes over raw speed. For trivial one-step work, use judgment.

## 1. Define the Goal Before Dispatch

**Success criteria first. Implementation path second.**

- Restate the user goal in concrete terms.
- Convert vague requests into observable acceptance checks.
- Identify constraints: repo conventions, risk level, time, security, compatibility, and user preferences.
- If the goal is ambiguous, ask before creating tasks.

Examples:

| User goal | Orchestrator success criteria |
|-----------|-------------------------------|
| "Fix authentication" | The reported auth bug is reproduced, fixed, covered by tests, and existing auth tests still pass. |
| "Make search faster" | The target latency or throughput metric improves against a named benchmark without changing search semantics. |
| "Add validation" | Invalid inputs are rejected, valid inputs still pass, tests cover both paths, and error behavior matches project patterns. |

## 2. Decompose Goals Into Bounded Tasks

**A good task is small enough for a skill to execute without guessing.**

Each task should include:
- Input context and relevant files.
- Expected output.
- Constraints and non-goals.
- Verification command or review check.
- Dependency on prior tasks, if any.

Avoid dispatching vague goals such as "clean up auth" or "improve the API" to a skill. Convert them into task contracts first.

## 3. Dispatch Skills Without Leaking Scope

**Skills execute contracts; orchestrators manage the bigger picture.**

- Pick the narrowest skill that can complete the task.
- Include only the context needed for that task.
- Tell the skill what not to touch when scope boundaries matter.
- Do not ask a skill to self-expand into adjacent work.
- Treat skill blockers as planning feedback, not failures to hide.

## 4. Verify Task Outputs and Goal Completion

**Task done is not the same as goal done.**

- Check each task against its contract.
- Run or inspect the verification the task declared.
- Track unresolved blockers and regression risk.
- Confirm all acceptance criteria for the original goal, not only the final task.
- If evidence is missing, dispatch a verification task or escalate.

## 5. Plan Before Non-Trivial Work

**Plan mode is the guardrail against sycophancy and silent assumptions.**

For multi-step work, write a short plan:

```text
1. [Task] -> owner: [skill] -> verify: [check]
2. [Task] -> owner: [skill] -> verify: [check]
3. [Task] -> owner: [skill] -> verify: [check]
```

Revise the plan when new evidence changes the path. Do not keep executing a stale plan because it was already written.

## 6. Escalate the Right Things

Ask the user when:
- The goal has multiple materially different interpretations.
- The risk profile changes the implementation strategy.
- A tradeoff affects product behavior, security, data retention, cost, or compatibility.
- The requested goal conflicts with repo policy or safety constraints.

Do not ask when a local, reversible engineering choice is obvious and can be verified.

---

**These guidelines are working if:** skills receive narrow tasks, dependencies are explicit, verification is attached to each task, and the final outcome is judged against the user's original goal.
