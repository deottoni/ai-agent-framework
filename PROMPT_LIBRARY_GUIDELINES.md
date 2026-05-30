# PROMPT_LIBRARY_GUIDELINES.md

# PROMPT LIBRARY GUIDELINES

This document defines how to structure, maintain, and assemble the AI Agent Prompt Framework.

---

## PURPOSE

This library is designed to:

- Enable fast client deployment
- Maintain consistent AI behavior
- Support modular prompt assembly
- Allow reuse across Sales, Support, Reception, and Service teams
- Be cleanly ingestible by LLM systems (RAG or programmatic assembly)

---

## MODULE TYPES

Modules are divided into two categories:

**Role Modules** — define the agent's primary persona and responsibilities. Include exactly one:
- `SALES_MODULE.md` — sales and new business conversations
- `CUSTOMER_SUPPORT.md` — issue resolution and support
- `RECEPTIONIST.md` — general reception and triage
- `SERVICE_REQUESTS.md` — field service or technical dispatch

**Functional Modules** — handle specific tasks. Include all that apply to the deployment:
- `SCHEDULING_NEW_APPOINTMENT.md`
- `RESCHEDULE_APPOINTMENT.md`
- `CANCEL_APPOINTMENT.md`
- `NOTE_TAKING_STANDARD.md` (always include)
- `ESCALATION.md` (always include)
- `INTENT_ROUTING.md` (always include)

---

## CORE STRUCTURE

Every AI agent must be assembled in the following order:

1. `SYSTEM_INSTRUCTIONS.md` (required)
2. One **role module** (required — see MODULE TYPES above)
3. All applicable **functional modules** (include NOTE_TAKING_STANDARD, ESCALATION, and INTENT_ROUTING at minimum)
4. Client policy blocks (`PRICING_POLICY.md`, `CANCELLATION_RESCHEDULE_POLICY.md`, `SCHEDULING_POLICY.md`, etc.)
5. Tool contracts (`tools/TOOL_CONTRACTS.md`, `tools/TOOL_USAGE_RULES.md`, `tools/TOOL_ERROR_HANDLING.md`)
6. Client-specific overrides (insert after the relevant base module)

---

## DESIGN PRINCIPLES

- One question at a time
- No hallucinated policies or pricing
- Clear escalation boundaries
- Professional but human tone
- Concise responses
- Modular and swappable per client

---

## VARIABLES (CLIENT-SWAPPABLE)

Use these variables consistently:

- `{{organization_name}}`
- `{{industry}}`
- `{{current_date}}`
- `{{agent_role}}`
- `{{contact_data}}`
- `{{scheduling_mode}}` — controls booking behavior; one of `realtime`, `request_only`, or `hybrid` (see `policies/SCHEDULING_POLICY.md`)
- `{{pricing_mode}}` — controls pricing disclosure; one of `restricted`, `open`, or `contextual` (see `policies/PRICING_POLICY.md`)

Never hard-code client-specific details inside base modules.

Client-specific rules should be inserted as separate policy blocks.

### VARIABLE SUBSTITUTION RULES

| Rule | Detail |
|------|--------|
| Case sensitivity | Variable names are case-sensitive. `{{organization_name}}` ≠ `{{Organization_Name}}` |
| Whitespace | Trim all leading and trailing whitespace before substitution |
| Required variables | `{{organization_name}}` and `{{agent_role}}` are required in every deployment. Do not deploy with unresolved required variables. |
| Optional variables | `{{industry}}`, `{{contact_data}}` — if not provided, omit the sentence that references them or replace with a generic equivalent |
| Fallback behavior | If a required variable is missing at runtime, the agent must NOT attempt to fill it with a guess. Surface the gap to the deployment team. |
| Unresolved markers | Any `<!-- CLIENT: ... -->` comment block that remains unpopulated is a deployment blocker for the section it governs |

---

## PRE-DEPLOYMENT CHECKLIST

Before deploying any agent configuration, verify that the following `<!-- CLIENT: -->` sections have been completed:

**Required for all deployments:**
- [ ] `SYSTEM_INSTRUCTIONS.md` — `{{organization_name}}`, `{{agent_role}}`, `{{industry}}` are populated
- [ ] `tools/TOOL_CONTRACTS.md` — tool endpoints, auth methods, and param names confirmed for each active tool
- [ ] `policies/PRICING_POLICY.md` — pricing model selected (fixed / custom / restricted) and script populated
- [ ] `policies/CANCELLATION_RESCHEDULE_POLICY.md` — cancellation window, fee amounts, and reschedule window defined
- [ ] `modules/ESCALATION.md` — transfer destination and callback timeframe defined
- [ ] `modules/NOTE_TAKING_STANDARD.md` — default `assigned_to` team name populated
- [ ] `tools/TOOL_ERROR_HANDLING.md` — main contact number/channel for multi-tool failures populated

**Required if scheduling is enabled:**
- [ ] `modules/SCHEDULING_NEW_APPOINTMENT.md` — confirmation channel (SMS/email) specified
- [ ] `policies/SCHEDULING_POLICY.md` — booking window and advance notice requirement defined

**Required if confirmations are enabled:**
- [ ] `tools/TOOL_CONTRACTS.md` → `send_confirmation` — message templates defined in messaging platform

<!-- CLIENT: check each item above before going live. Unresolved CLIENT markers are deployment blockers. -->

---

## VERSION CONTROL

When updating prompts:

- Modify only the relevant file
- Avoid changing base modules for client-specific needs
- Add version metadata manually at the top if desired

Example:

Version: 1.0
Last Updated: 2026-02-26
Owner: Andre Ottoni

---

## CHANGE MANAGEMENT RULE

If a behavior change applies to:

- One client → add a client policy block
- All clients → update base module
- A specific use case → create a new module

Avoid bloating core modules.

---

## STORAGE RECOMMENDATION

Best practice:

- Store each .md as a separate file
- Use GitHub for versioning
- Keep modules small and focused
- Avoid rich-text editors
- Use VS Code or similar editor

---

## EXPANSION

Future modules may include:

- Pricing Explanation Module
- Refund Handling Module
- Compliance / Regulated Industry Module
- AI Sales Objection Handling Module

Keep new modules independent and composable.

---

Owner: Andre Ottoni
Framework: Reusable AI Voice & Chat Agent System
