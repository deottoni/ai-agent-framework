# Internal Ops Agent Library

A small, opinionated library of Claude Code subagents for running a small business — not talking
to its customers (see `../customer-facing/` for that), but standing in for the advisors, analysts,
and specialists most small businesses can't afford to hire full-time: a strategic sounding board,
an ops manager, a sales coach, an HR partner, a finance analyst, a data analyst, a marketing
strategist, a support lead.

## Directory structure

```
internal-ops/
├── AGENT_ANATOMY.md     # the methodology — 6-part shape every agent follows, and how to build your own
├── GETTING_STARTED.md   # first-timer setup guide
└── agents/               # the 8 worked examples, ready to copy into .claude/agents/
```

## Why these two things are separate repos-in-one

`../customer-facing/` is a templated prompt library assembled into one big system prompt and
deployed to a voice/chat platform — it represents your business talking to *its* customers.
This is a set of Claude Code subagents that represent *you*, talking to Claude. Different
mechanism, different audience, different file shape — worth keeping visually and structurally
separate rather than blurring into one undifferentiated pile of "agents."

## Quick start

Read `GETTING_STARTED.md`, pick an agent from `agents/` that matches your biggest bottleneck,
copy it into `.claude/agents/`, tailor it to your real business. `AGENT_ANATOMY.md` explains the
reasoning if you want to build one that isn't in the starting 8.

---

*Framework by Andre Ottoni — andreottoni.com*
