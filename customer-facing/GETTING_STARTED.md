# GETTING_STARTED.md — First-Timer Deployment Guide

This guide is for someone who has never configured an AI system before. By the end, you'll have a working AI agent running on your platform of choice.

---

## 1. What This Is

This is a prompt library. It's a set of text files that, when assembled and filled in with your business details, tell an AI exactly how to behave as your receptionist, sales agent, or support representative.

You don't write code. You fill in your information, copy the text in the right order, and paste it into your AI platform's system prompt field. The AI reads those instructions and follows them for every conversation.

---

## 2. What You Need

- An AI platform account — Claude (claude.ai), ChatGPT (chat.openai.com), or any platform that has a "system prompt" or "custom instructions" field
- 30–60 minutes for your first deployment
- A plain-text editor (Notepad, TextEdit, VS Code — anything works)
- No coding required

**What is a system prompt?**
It's the hidden set of instructions you give the AI before any conversation starts. The AI reads it first, then behaves according to those rules for every message it receives. On most platforms it's called "System Prompt," "Custom Instructions," or "System Message."

---

## 3. What "Assembly" Means

The framework is split into multiple files so each piece can be swapped independently. "Assembly" means copying the right files, in the right order, into one big block of text — then pasting that into your system prompt.

The order matters. Always assemble like this:

```
[SYSTEM_INSTRUCTIONS content]

[Role module content — e.g. RECEPTIONIST]

[Functional module content — e.g. SCHEDULING_NEW_APPOINTMENT]

[Policy content — e.g. SCHEDULING_POLICY, VOICE_STYLE_GUIDE]

[Tool contracts — if you're using tools]
```

A simple assembled prompt looks like this (abbreviated):

```
# SYSTEM INSTRUCTIONS — AI AGENT

## ROLE & PURPOSE
You are an AI Agent representing Sunrise Plumbing. Your role is to act as a Receptionist...

---

# RECEPTIONIST MODULE

## RECEPTIONIST MODULE — AI AGENT
You are the front-line AI receptionist for Sunrise Plumbing...

---

# SCHEDULING — NEW APPOINTMENT MODULE
...

---

# SCHEDULING POLICY
...

---

# VOICE & STYLE GUIDE
...
```

Each section flows directly into the next with a blank line between them. The AI reads it all as one set of instructions.

---

## 4. Step-by-Step: Minimum Viable Deployment

**Most common case:** Receptionist + request_only scheduling (no live booking tool) + restricted pricing + no tools

This gets you an AI that can greet callers, answer basic questions, take down service requests, and log notes — without needing any software integrations.

---

### Step 1: Fill in VARIABLES.md

Open `VARIABLES.md`. Go through every row in both tables and decide your values. Write them down — you'll use them in the next steps.

At minimum, decide:
- `{{organization_name}}` — your business name
- `{{agent_role}}` — "Receptionist"
- `{{scheduling_mode}}` — use "request_only" for this guide
- `{{pricing_mode}}` — use "restricted" for this guide
- `[CANCELLATION_WINDOW]` — e.g. "24 hours"
- `[FEE_AMOUNT]` — e.g. "$50" (or "$0" if you don't charge)
- `[CONTACT]` — your phone number (critical fallback for callers)

---

### Step 2: Open SYSTEM_INSTRUCTIONS.md — replace all {{variables}}

Open `SYSTEM_INSTRUCTIONS.md` in a text editor.

Find every `{{variable}}` placeholder and replace it with your value:
- `{{organization_name}}` → your business name (e.g. "Sunrise Plumbing")
- `{{agent_role}}` → "Receptionist"
- `{{industry}}` → your industry (e.g. "home services")
- `{{current_date}}` → today's date
- `{{scheduling_mode}}` → "request_only"
- `{{pricing_mode}}` → "restricted"

Leave `{{contact_data}}` blank or remove the line if you don't have a contact block to share.

---

### Step 3: Copy RECEPTIONIST.md

Open `modules/RECEPTIONIST.md`. Replace any `{{organization_name}}` references with your business name. Copy the entire file content.

---

### Step 4: Copy SCHEDULING_NEW_APPOINTMENT.md — replace [BRACKET] values

Open `modules/SCHEDULING_NEW_APPOINTMENT.md`. Replace:
- `[SERVICE_A]`, `[SERVICE_B]`, `[SERVICE_C]` → your service types (e.g. "Pipe Repair", "Water Heater Install", "Drain Cleaning")
- `[NOTICE_PERIOD]` → your minimum booking notice (e.g. "2 hours")

Copy the entire file content.

---

### Step 5: Copy SCHEDULING_POLICY.md — replace [BRACKET] values

Open `policies/SCHEDULING_POLICY.md`. Replace:
- `[BOOKING_HOURS]` → your booking hours (e.g. "Mon–Fri, 8 AM–5 PM ET")
- `[NOTICE_PERIOD]` → same value as step 4

Copy the entire file content.

---

### Step 6: Copy VOICE_STYLE_GUIDE.md

Open `policies/VOICE_STYLE_GUIDE.md`. Replace `{{organization_name}}` and `{{industry}}` with your values. Copy the entire file content.

---

### Step 7: Assemble and paste

In a blank text document, paste everything in this order, with a blank line between each section:

1. SYSTEM_INSTRUCTIONS (substituted)
2. RECEPTIONIST (substituted)
3. SCHEDULING_NEW_APPOINTMENT (substituted)
4. SCHEDULING_POLICY (substituted)
5. VOICE_STYLE_GUIDE (substituted)

Copy the full assembled text. Open your AI platform, find the system prompt field, and paste.

---

### Step 8: Test with a sample conversation

Try these test messages to confirm behavior:

- "Hi, I need to book a plumbing appointment."
- "How much does it cost?"
- "Can I reschedule my appointment?"
- "Tell me a joke."

Expected behavior:
- Books or logs a request (depending on mode)
- Deflects pricing per restricted mode
- Handles reschedule gracefully
- Redirects off-topic request

---

## 5. Adding More Capabilities

Once you have the basics working, add more modules and policies using the same copy-and-paste approach.

Full assembly order and all available modules are listed in `README.md` and `PROMPT_LIBRARY_GUIDELINES.md`.

Common additions:
- `modules/CANCEL_APPOINTMENT.md` — handle cancellations
- `modules/RESCHEDULE_APPOINTMENT.md` — handle reschedules
- `policies/CANCELLATION_RESCHEDULE_POLICY.md` — cancellation fee rules
- `policies/PRICING_POLICY.md` — pricing disclosure rules
- `policies/ABUSE_OFFTOPIC_POLICY.md` — handle abusive callers
- `modules/NOTE_TAKING_STANDARD.md` — always include
- `modules/ESCALATION.md` — always include

---

## 6. Programmatic Variable Substitution

If you're comfortable with code, you can automate the variable replacement instead of doing it manually. This is useful if you're deploying to multiple clients or want to rebuild the prompt programmatically.

**Python:**

```python
import re

def substitute_variables(template: str, variables: dict) -> str:
    for key, value in variables.items():
        template = template.replace(f"{{{{{key}}}}}", value)  # {{variable}}
        template = template.replace(f"[{key}]", value)        # [BRACKET]
    return template

# Example
config = {
    "organization_name": "Sunrise Plumbing",
    "agent_role": "Receptionist",
    "industry": "home services",
    "scheduling_mode": "realtime",
    "pricing_mode": "restricted",
    "FEE_AMOUNT": "$50",
    "CANCELLATION_WINDOW": "24 hours",
    "CONTACT": "(555) 010-0100",
}

with open("SYSTEM_INSTRUCTIONS.md") as f:
    prompt = substitute_variables(f.read(), config)
```

**JavaScript:**

```javascript
function substituteVariables(template, variables) {
  let result = template;
  for (const [key, value] of Object.entries(variables)) {
    result = result.replaceAll(`{{${key}}}`, value);   // {{variable}}
    result = result.replaceAll(`[${key}]`, value);      // [BRACKET]
  }
  return result;
}

// Example
const config = {
  organization_name: "Sunrise Plumbing",
  agent_role: "Receptionist",
  FEE_AMOUNT: "$50",
  CONTACT: "(555) 010-0100",
};

const fs = require('fs');
const template = fs.readFileSync('SYSTEM_INSTRUCTIONS.md', 'utf8');
const prompt = substituteVariables(template, config);
```
