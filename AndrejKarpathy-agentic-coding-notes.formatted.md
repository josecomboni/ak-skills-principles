# Andrej Karpathy — Agentic Coding Notes

> A few random notes from claude coding quite a bit last few weeks.
>
> **Source:** [@karpathy on X](https://x.com/karpathy/status/2015883857489522876)

---

## 1. Coding Workflow

Given the latest lift in LLM coding capability, like many others I rapidly went from about **80% manual+autocomplete coding** and **20% agents** in November to **80% agent coding** and **20% edits+touchups** in December. I really am mostly programming in English now, a bit sheepishly telling the LLM what code to write... in words.

It hurts the ego a bit but the power to operate over software in large "code actions" is just too net useful — especially once you adapt to it, configure it, learn to use it, and wrap your head around what it can and cannot do.

This is easily the biggest change to my basic coding workflow in ~2 decades of programming and it happened over the course of a few weeks. I'd expect something similar to be happening to well into double digit percent of engineers out there, while the awareness of it in the general population feels well into low single digit percent.

---

## 2. IDEs, Agent Swarms, and Fallibility

Both the "no need for IDE anymore" hype and the "agent swarm" hype is, imo, too much for right now. The models definitely still make mistakes and if you have any code you actually care about I would **watch them like a hawk**, in a nice large IDE on the side.

### How the mistakes have changed

They are not simple syntax errors anymore — they are **subtle conceptual errors** that a slightly sloppy, hasty junior dev might make. The most common categories:

- They **make wrong assumptions** on your behalf and just run along with them without checking.
- They **don't manage their confusion** — they don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should.
- They are still a little too **sycophantic**.
- They like to **overcomplicate code and APIs**, bloat abstractions, and don't clean up dead code after themselves.
- They will implement an inefficient, bloated, brittle construction over 1000 lines of code — and it's up to you to be like "umm couldn't you just do this instead?" and they will be like "of course!" and immediately cut it down to 100 lines.
- They still sometimes **change/remove comments and code** they don't like or don't sufficiently understand as side effects, even if it's orthogonal to the task at hand.

All of this happens despite a few simple attempts to fix it via instructions in `CLAUDE.md`. Things get better in plan mode, but there is some need for a lightweight inline plan mode.

> **TLDR:** Everyone has their developing flow. My current is a small few CC sessions on the left in ghostty windows/tabs and an IDE on the right for viewing the code + manual edits.

---

## 3. Tenacity

It's so interesting to watch an agent relentlessly work at something. They **never get tired**, they **never get demoralized**, they just keep going and trying things where a person would have given up long ago to fight another day.

It's a "feel the AGI" moment to watch it struggle with something for a long time just to come out victorious 30 minutes later. You realize that **stamina is a core bottleneck to work** and that with LLMs in hand it has been dramatically increased.

---

## 4. Speedups

It's not clear how to measure the "speedup" of LLM assistance. Certainly I feel net way faster at what I was going to do, but the main effect is that I do **a lot more** than I was going to do because:

1. I can code up all kinds of things that just wouldn't have been worth coding before.
2. I can approach code that I couldn't work on before because of knowledge/skill issues.

So certainly it's a speedup, but it's possibly a lot more an **expansion**.

---

## 5. Leverage

LLMs are exceptionally good at **looping until they meet specific goals** — this is where most of the "feel the AGI" magic is to be found.

### Key techniques

- **Don't tell it what to do** — give it success criteria and watch it go.
- Get it to **write tests first** and then pass them.
- Put it in the loop with a **browser MCP**.
- Write the **naive algorithm** that is very likely correct first, then ask it to optimize it while preserving correctness.
- Change your approach from **imperative to declarative** to get the agents looping longer and gain leverage.

---

## 6. Fun

I didn't anticipate that with agents, programming feels **more** fun because a lot of the fill-in-the-blanks drudgery is removed and what remains is the creative part.

I also feel less blocked/stuck (which is not fun) and I experience a lot more courage because there's almost always a way to work hand in hand with it to make some positive progress.

I have seen the opposite sentiment from other people too: LLM coding will split engineers based on those who primarily liked **coding** vs. those who primarily liked **building**.

---

## 7. Atrophy

I've already noticed that I am slowly starting to **atrophy my ability to write code manually**.

Generation (writing code) and discrimination (reading code) are different capabilities in the brain. Largely due to all the little mostly syntactic details involved in programming, you can review code just fine even if you struggle to write it.

---

## 8. Slopacolypse

I am bracing for 2026 as the year of the **slopacolypse** across all of GitHub, Substack, arXiv, X/Instagram, and generally all digital media.

We're also going to see a lot more **AI hype productivity theater** (is that even possible?), on the side of actual, real improvements.

---

## 9. Open Questions

A few of the questions on my mind:

- What happens to the **"10X engineer"** — the ratio of productivity between the mean and the max engineer? It's quite possible that this grows *a lot*.
- Armed with LLMs, do **generalists increasingly outperform specialists**? LLMs are a lot better at fill in the blanks (the micro) than grand strategy (the macro).
- What does LLM coding **feel like in the future**? Is it like playing StarCraft? Playing Factorio? Playing music?
- How much of society is **bottlenecked by digital knowledge work**?

---

## TLDR

LLM agent capabilities (Claude & Codex especially) have crossed some kind of **threshold of coherence** around December 2025 and caused a **phase shift** in software engineering and closely related work.

The intelligence part suddenly feels quite a bit ahead of all the rest of it — integrations (tools, knowledge), the necessity for new organizational workflows, processes, diffusion more generally.

**2026 is going to be a high energy year as the industry metabolizes the new capability.**

---

**Reference:** [https://x.com/karpathy/status/2015883857489522876](https://x.com/karpathy/status/2015883857489522876)
