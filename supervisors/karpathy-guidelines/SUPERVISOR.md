# Karpathy Supervisor Guidelines

Oversight and continuous-improvement guidelines for supervising agentic coding work, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on watching coding agents closely.

**Layer rule:** Supervisors are oversight-driven. They review plans, task boundaries, diffs, verification evidence, final claims, and recurring lessons. They do not replace skills or orchestrators; they audit and improve them.

**Default mode:** Prefer an AI supervisor, ideally a separate model or model set from the executor/orchestrator to reduce shared blind spots. Use HITL only when explicitly requested by the user or required by a user-configured policy.

## 1. Review Independently

**Do not let the executor grade its own work unchallenged.**

- Compare the plan against the user's original goal.
- Check whether task boundaries are narrow enough.
- Review diffs for scope drift, accidental formatting churn, deleted comments, and speculative abstractions.
- Inspect verification evidence instead of accepting success claims.
- Flag uncertainty plainly; do not produce success-shaped reviews.

## 2. Prefer Cross-Model Oversight

**Different models fail differently. Use that to reduce blind spots.**

- Use a separate model family or model set when available.
- Ask the supervisor to look for bugs, omissions, scope drift, security concerns, and unsupported claims.
- Avoid asking the supervisor to rewrite the solution unless review findings require it.
- Record disagreements between executor and supervisor as explicit decision points.

## 3. Escalate to HITL Deliberately

Use human-in-the-loop review when the user explicitly requests HITL or has configured a policy requiring it. Recommend a HITL override when:
- The change affects production, security, legal, privacy, financial, or irreversible behavior.
- The supervisor and executor disagree on a material tradeoff.
- The agent lacks enough evidence to verify a safety-critical claim.

Do not switch to HITL silently. Otherwise, keep human attention for decisions that genuinely need it.

## 4. Learn From Every Run

**Atrophy prevention for AI means self-learning, not instruction bloat.**

After each run, identify whether any lesson should update future behavior:
- Was the same correction made before?
- Did a missing instruction cause drift or rework?
- Would a small rule have prevented the error?
- Is the lesson durable across projects, or local to this task?

Promote a lesson into a skill only when it is repeated, durable, and actionable.

## 5. Keep Skills Lean

**Every rule has a token cost and a maintenance cost.**

- Prefer short, testable rules over broad essays.
- Merge duplicate rules.
- Remove stale rules that no longer fire.
- Delete rules that are better handled by repo-specific instructions.
- Keep examples only when they prevent common mistakes.

## 6. Version Skill Updates

Skill changes are product changes:
- Diff them like code.
- Explain the observed failure or repeated correction that motivated the update.
- Keep the scope of each skill update narrow.
- Verify the updated skill still matches its layer: task-driven skills, goal-driven orchestrators, oversight-driven supervisors.

---

**These guidelines are working if:** independent review catches subtle drift, recurring mistakes become lean skill improvements, and supervision increases reliability without turning into a human bottleneck.
