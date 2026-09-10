# GETTING_STARTED.md — first-timer setup guide

This guide is for someone who's used Claude but never set up a custom agent before. By the end,
you'll have a working advisor Claude can become on demand, built around your own business.

---

## 1. What this is

These are Claude Code **subagents** — persona files Claude actually runs as when a task matches,
each with its own instructions and its own model tier. Unlike the customer-facing framework (see
`../customer-facing/`), nobody outside your business ever talks to these — they're advisors for
you and your team.

## 2. What you need

- **Claude Code** or **Cowork** — subagents are a real mechanism there (Claude reads the file and
  runs as that persona). On claude.ai, there's no subagent system yet; you can still use these as
  reference text pasted into a Project's instructions, but the model-tier switching won't be
  automatic.
- 10–15 minutes for your first one.
- No coding required — these are plain text files.

## 3. Install one

1. Pick the agent that matches your bottleneck from `agents/` (see the table in that folder's
   README).
2. Copy it into your own project at `.claude/agents/[name].md`, keeping the filename.
3. Open it and tailor the persona — swap in your real business context, your team's names, the
   specific problem you're pointing it at. The scope, guardrails, and example prompts are a solid
   default; the tailoring is what makes it feel built for you, not generic.

## 4. Use it

In Claude Code or Cowork, just describe the task — Claude picks up the matching subagent
automatically when the description fits. You can also name it directly: *"as the ops-manager,
help me fix our scheduling process."*

## 5. Adapt or build a new one

If none of the 8 fit your actual bottleneck, don't force one to stretch — read
`AGENT_ANATOMY.md` and build your own using the same six-part shape. It's a short read and the
part most worth doing carefully is the scope's "not" half.

---

*Framework by Andre Ottoni — andreottoni.com*
