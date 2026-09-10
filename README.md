# AI Agent Framework

Two libraries of production-ready AI agents, built and maintained by Andre Ottoni — split by who
the agent actually talks to.

| | Talks to | Mechanism | |
|---|---|---|---|
| **[`customer-facing/`](customer-facing/)** | Your customers — the people who call or message your business | A templated system prompt, assembled and deployed to a voice/chat platform (Claude, ChatGPT, or any platform with a system-prompt field) | Sales, Customer Support, Receptionist, Service Requests |
| **[`internal-ops/`](internal-ops/)** | You and your team | Claude Code / Cowork subagents (`.claude/agents/*.md`) — real, invokable personas, not assembled text | CEO Advisor, Ops Manager, Marketing Strategist, Sales Coach, HR Partner, Finance Analyst, Support Lead, Data Analyst |

They're kept in one repo on purpose — same author, same quality bar, same underlying question
("what does a well-designed agent actually look like"), just answered for two different jobs. Each
folder is fully self-contained with its own README, getting-started guide, and methodology
document — read the one that matches what you're trying to build.

## Which one do I want?

- Building something your *customers* will talk to (a phone agent, a chat widget, a receptionist
  bot)? → `customer-facing/`
- Want an advisor, analyst, or specialist *you or your team* can talk to inside Claude — a
  strategic sounding board, a finance sanity-check, an HR partner? → `internal-ops/`

---

*Framework by Andre Ottoni — andreottoni.com*
