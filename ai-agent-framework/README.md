# AI Agent Prompt Framework

A modular, production-ready prompt library for AI voice and chat agents. Supports Sales, Customer Support, Receptionist, and Service Request roles across any industry. Designed for fast client deployment, LLM ingestion, and RAG-based retrieval.

---

## Directory Structure

```
ai-agent-framework/
│
├── SYSTEM_INSTRUCTIONS.md          # Required base layer for every agent
├── PROMPT_LIBRARY_GUIDELINES.md    # Assembly guide, module types, pre-deploy checklist
├── INTERNAL_NOTES.md               # Maintainer notes — NOT included in agent assembly
│
├── modules/                        # Functional prompt modules
│   ├── INTENT_ROUTING.md           # Routes every conversation turn to the correct module
│   ├── RECEPTIONIST.md             # General greeting, triage, and message-taking
│   ├── SALES_MODULE.md             # Lead qualification and next-step routing
│   ├── CUSTOMER_SUPPORT.md         # Issue resolution and escalation
│   ├── SERVICE_REQUESTS.md         # Field service / dispatch intake
│   ├── SCHEDULING_NEW_APPOINTMENT.md
│   ├── RESCHEDULE_APPOINTMENT.md
│   ├── CANCEL_APPOINTMENT.md
│   ├── ESCALATION.md               # Human handoff triggers and procedures
│   └── NOTE_TAKING_STANDARD.md     # Structured note format for all create_note calls
│
├── policies/                       # Client-configurable rules
│   ├── SCHEDULING_POLICY.md        # Booking windows, modes, conflict handling
│   ├── CANCELLATION_RESCHEDULE_POLICY.md
│   ├── PRICING_POLICY.md           # Pricing modes and authorized response scripts
│   ├── PAYMENTS_POLICY.md          # Payment capture and dispute handling
│   ├── PRIVACY_SECURITY_POLICY.md  # PII rules and identity verification
│   ├── VOICE_STYLE_GUIDE.md        # Tone, phrasing, and example exchanges
│   └── ABUSE_OFFTOPIC_POLICY.md    # Off-topic redirects and abuse tiers
│
└── tools/                          # Tool behavioral contracts
    ├── TOOL_CONTRACTS.md            # Parameters and response schemas for all tools
    ├── TOOL_USAGE_RULES.md          # When and how tools may be called
    └── TOOL_ERROR_HANDLING.md       # Error categories, retry policy, fallback cascade
```

---

## Key Concepts

### Scheduling Modes

Set via `{{scheduling_mode}}` in client configuration. Controls whether the agent can confirm bookings in-call.

| Mode | Behavior |
|------|----------|
| `realtime` | Invokes `book_appointment` in-call. Confirms only when tool returns `confirmation_id`. |
| `request_only` | Collects fields and logs via `create_note`. Human team books manually. Never says "confirmed." |
| `hybrid` | Attempts realtime booking; falls back to `request_only` if tool fails. |

**Safe default:** `request_only` (no tool invoked, no false confirmations).

### Pricing Modes

Set via `{{pricing_mode}}` in client configuration. Controls what the agent may say about cost.

| Mode | Behavior |
|------|----------|
| `restricted` | Never quotes any price. Always redirects to the human team. |
| `open` | Quotes prices listed verbatim in `policies/PRICING_POLICY.md` EXCEPTIONS section. |
| `contextual` | May share an authorized range if listed in policy. Never presents range as a guarantee. |

**Safe default:** `restricted` (no pricing quoted).

### Tool Safety

- The agent never claims success unless the tool returns the corresponding success status and ID.
- A timeout or non-response is not a success.
- Side-effect tools (`book_appointment`, `cancel_appointment`, `reschedule_appointment`, `send_confirmation`) require explicit caller confirmation before invocation.
- `book_appointment` must not be invoked in `request_only` mode.

---

## Assembly Guide

Every agent is assembled in this order:

```
1. SYSTEM_INSTRUCTIONS.md               (always required)
2. One role module                       (pick one)
   └── RECEPTIONIST / SALES_MODULE / CUSTOMER_SUPPORT / SERVICE_REQUESTS
3. Functional modules                    (include all that apply)
   ├── INTENT_ROUTING.md                 (always recommended)
   ├── NOTE_TAKING_STANDARD.md           (always recommended)
   ├── ESCALATION.md                     (always recommended)
   ├── SCHEDULING_NEW_APPOINTMENT.md
   ├── RESCHEDULE_APPOINTMENT.md
   └── CANCEL_APPOINTMENT.md
4. Policies                              (include all that apply)
5. Tool contracts                        (if tools are enabled)
   ├── tools/TOOL_CONTRACTS.md
   ├── tools/TOOL_USAGE_RULES.md
   └── tools/TOOL_ERROR_HANDLING.md
```

For retrieval-based (RAG) systems: store each file as an independent chunk. The agent retrieves relevant modules per conversation turn.

---

## Variables

Every base module uses `{{variable}}` placeholders. Replace before deployment.

| Variable | Required | Description |
|----------|----------|-------------|
| `{{organization_name}}` | Yes | Client's business name |
| `{{agent_role}}` | Yes | The agent's role (e.g., "Receptionist", "Sales Agent") |
| `{{industry}}` | Recommended | Industry context (e.g., "home services", "healthcare") |
| `{{current_date}}` | Recommended | Today's date, injected at runtime |
| `{{contact_data}}` | Optional | Business phone, email, or address |
| `{{scheduling_mode}}` | Yes if scheduling enabled | `realtime` / `request_only` / `hybrid` |
| `{{pricing_mode}}` | Yes if pricing discussed | `restricted` / `open` / `contextual` |

Never hard-code client-specific values inside base module files. Use client policy blocks instead.

---

## Pre-Deployment Checklist

Before going live, confirm these are populated:

- [ ] `{{organization_name}}` and `{{agent_role}}` resolved in `SYSTEM_INSTRUCTIONS.md`
- [ ] `{{scheduling_mode}}` set in `policies/SCHEDULING_POLICY.md`
- [ ] `{{pricing_mode}}` set in `policies/PRICING_POLICY.md`
- [ ] Cancellation window and fee amounts defined in `policies/CANCELLATION_RESCHEDULE_POLICY.md`
- [ ] Tool endpoints, auth, and param names confirmed in `tools/TOOL_CONTRACTS.md`
- [ ] `assigned_to` default team set in `modules/NOTE_TAKING_STANDARD.md`
- [ ] Escalation transfer destination set in `modules/ESCALATION.md`
- [ ] Contact fallback (phone/email) set in `tools/TOOL_ERROR_HANDLING.md`
- [ ] No unresolved `<!-- CLIENT: -->` blocks in any active module

---

## Design Principles

- **One question at a time** — enforced across all modules
- **No hallucinated policies, pricing, or availability** — agent reads only from configured data
- **Tool success = explicit confirmation** — never infer success from silence or timeout
- **Graceful degradation** — every tool failure has a fallback path via `create_note`
- **Voice-friendly** — short sentences, read-back confirmations, no numeric date formats verbally
- **Platform-agnostic** — no vendor-specific tool names; abstract tool names only
- **Modular and swappable** — change one module without affecting others

---

## Tools Reference

| Tool | Type | Purpose |
|------|------|---------|
| `lookup_appointment` | Read-only | Find existing appointment by name + phone |
| `book_appointment` | Side-effect | Create new booking |
| `reschedule_appointment` | Side-effect | Move existing booking |
| `cancel_appointment` | Side-effect | Cancel existing booking |
| `send_confirmation` | Side-effect | Send SMS or email confirmation |
| `create_note` | Side-effect | Log structured note for team follow-up |
| `transfer_call` | Side-effect | Transfer caller to human agent |

Full parameter schemas, response contracts, and `delivery_status` handling are in `tools/TOOL_CONTRACTS.md`.

---

## Versioning

- Modify only the relevant file per change
- Client-specific changes go in policy blocks — never in base modules
- Track breaking changes in commit history
- See `PROMPT_LIBRARY_GUIDELINES.md` for full change management rules

---

Owner: Andre Ottoni
Version: 1.0.0 — Last updated: 2026-02-26
