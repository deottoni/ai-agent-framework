# agents/

8 worked examples of the anatomy described in `../AGENT_ANATOMY.md` — one per common internal
business function (leadership, ops, marketing, sales, HR, finance, support, data). Each is a
real, usable Claude Code subagent (`name`/`description`/`model` frontmatter + persona + scope +
standing instructions + example prompts), not a hypothetical illustration.

Copy one into your own project's `.claude/agents/[name].md`, then tailor it: fold in your actual
business context, your team's names, the specific problem you're using it for. The persona, scope,
and instructions here are a solid, opinionated default — the tailoring pass is what makes the
result feel built for *your* business instead of copy-pasted.

| Agent | Model | For |
|---|---|---|
| `ceo-advisor` | opus | High-stakes strategic calls — pricing, hiring, positioning |
| `ops-manager` | sonnet | Process, scheduling, day-to-day operations |
| `marketing-strategist` | sonnet | Campaigns, positioning, customer-facing copy |
| `sales-coach` | sonnet | Call prep, objection handling, proposals |
| `hr-partner` | sonnet | Hiring, people issues, basic policy |
| `finance-analyst` | opus | Budgets, forecasts, pricing math |
| `support-lead` | haiku | Customer replies, routine service questions |
| `data-analyst` | opus | Margin, revenue, and customer-pattern analysis |

See `../AGENT_ANATOMY.md` for why each is shaped the way it is, and the model-tier reasoning.
