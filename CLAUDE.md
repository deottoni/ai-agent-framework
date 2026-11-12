# AI Agent Framework — Router

Two self-contained libraries live here. Your job at the root is to send the user to the right one; each has its own README, getting-started guide and methodology doc.

Ask: **"Who will talk to this agent — your customers, or you and your team?"**

| Answer | Library | Start with |
|---|---|---|
| Customers (phone agent, chat widget, receptionist, sales, support) | `customer-facing/` | `customer-facing/GETTING_STARTED.md`, then `VARIABLES.md` and `EXAMPLE_DEPLOYMENT.md` |
| Me / my team (advisor, analyst, specialist inside Claude) | `internal-ops/` | `internal-ops/GETTING_STARTED.md` and `AGENT_ANATOMY.md` |

## Rules

- Don't mix the two mechanisms: customer-facing agents are an assembled system prompt deployed to a platform; internal-ops agents are Claude Code subagent files (`.claude/agents/*.md`).
- When editing a customer-facing module or policy, record the change in `customer-facing/CHANGELOG.md` and keep `metadata.yml` in sync.
- `archive/` is a frozen snapshot — don't edit it. Open work is in `TODO.md`.

## Related

To evaluate an agent or engineer its context, see [AI Builder OS](https://github.com/deottoni/ai-builder-os) (`evals/`, `context/`).
