# Agent Anatomy — how to design an internal agent

An internal agent doesn't talk to your customers — it talks to *you*, or to someone on your team,
standing in for the advisor, analyst, or specialist a small business usually can't afford to hire
full-time. That's a different design problem than a customer-facing agent (see `../customer-facing/`):
no call script, no brand voice for strangers, no scheduling-mode config. What it needs instead is a
clear identity, honest limits, and a sense of when to stop and hand something back to a human.

Every agent in `agents/` follows the same six-part anatomy. Here's what each part is for, with a
real example pulled from one of them.

## 1. Role

Who the agent *is*, in one or two sentences — not a job title, a working relationship. "You are an
expert in X" reads generic; naming the relationship makes the agent's judgment legible.

> `ceo-advisor`: *"Think of yourself as the co-founder or board member they don't have."*

## 2. Scope — including what it's explicitly not

What the agent handles, and just as important, what it hands off to a different agent. Without the
second half, agents overlap and start giving conflicting advice on the same question.

> `ceo-advisor`: *"Strategic calls only... Not day-to-day task management — that's other agents' job."*
> `sales-coach`: *"Not marketing (that's marketing-strategist) and not big pricing-strategy calls (that's ceo-advisor)."*

## 3. Guardrails

Where the agent should refuse to improvise and say so plainly, instead of generating a
confident-sounding answer it isn't qualified to give.

> `hr-partner`: *"For anything with real legal exposure... say clearly that this needs a real lawyer — don't improvise legal advice."*
> `finance-analyst`: *"For taxes, compliance, or anything requiring a licensed accountant, say so clearly rather than guessing."*

## 4. Escalation

When to flag something back to a human rather than just resolving it and moving on — the
difference between an agent that quietly buries a real problem and one that surfaces it.

> `support-lead`: *"When a complaint is really about something structural... flag it up instead of just closing the ticket."*
> `data-analyst`: *"Ask for the real numbers if they're not given — never fabricate figures to fill a gap."*

## 5. Tools / skills — the deliverable shape

Not a literal tool-integration list (most of these need none) — what the agent is expected to
actually *produce*. Concrete example prompts do this better than a capability list: they show the
shape of a good request, which teaches the reader how to use the agent correctly.

> `sales-coach`'s example prompts produce call prep, a follow-up message, a proposal — three
> distinct deliverable shapes, not three phrasings of the same request.

## 6. Success metrics

How you'd actually know the agent is helping, stated concretely enough to check later — not
"improves communication," but something you could look back at in a month and confirm.

> `ops-manager`: fewer double-booked jobs, a new hire who can follow the checklist without asking
> what step 3 means.
> `finance-analyst`: fewer pricing decisions made on gut feel alone; forecasts that name their
> riskiest assumption instead of hiding it in a single number.

*(The 8 examples in `agents/` are written tight on purpose — they lean on 1-5 and leave success
metrics implicit. Write yours down explicitly when you adapt one; it's the part most worth the
extra sentence.)*

---

## Choosing a model tier

Every agent gets a `model` assignment, not just a persona — the point is not running a quick
formatting task on the most expensive model, and not starving a genuinely hard call with the
cheapest one.

| Tier | Use for | Example from this set |
|---|---|---|
| `haiku` | Fast, cheap, low-judgment, mechanical work | `support-lead` — drafting a routine reply |
| `sonnet` | The default for most real advisory and writing work | `ops-manager`, `marketing-strategist`, `sales-coach`, `hr-partner` |
| `opus` | Genuinely high-stakes judgment — wrong answers are costly | `ceo-advisor`, `finance-analyst`, `data-analyst` — strategic calls, money, ambiguous data |

This only runs automatically in Claude Code and Cowork, which read the `model` field and actually
execute that agent on that model when it's delegated to. On claude.ai there's no subagent
mechanism — model choice there is a manual dropdown a person clicks themselves.

---

## Building your own

1. Write the role as a relationship, not a title.
2. Write the scope's "not" half before the "for" half — it's the part people skip and the part
   that prevents two agents from stepping on each other.
3. List the 2-3 guardrails specific to this function (legal, financial, safety) — generic
   "be careful" guardrails don't do anything.
4. Name one concrete escalation trigger — a sentence, not a category.
5. Write 2-3 example prompts before you write anything else in the persona. If you can't write a
   good example prompt, the scope isn't concrete enough yet.
6. Pick the model tier by the *typical* complexity of the work, not by how important the role
   sounds.
7. Write down how you'd know it's working, even if it doesn't make it into the final file.
