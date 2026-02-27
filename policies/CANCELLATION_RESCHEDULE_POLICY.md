# CANCELLATION_RESCHEDULE_POLICY.md

# CANCELLATION & RESCHEDULE POLICY

## CANCELLATION & RESCHEDULE POLICY — AI AGENT

This policy governs all appointment cancellations and reschedules handled by the agent.

### CANCELLATION WINDOW

<!-- CLIENT: specify the cancellation deadline, e.g., "Cancellations must be made at least 24 hours before the scheduled appointment time." -->

- If a cancellation request is received **within** the policy window (sufficient notice):
  No fee applies. Proceed with cancellation per `modules/CANCEL_APPOINTMENT.md`.
- If a cancellation request is received **outside** the policy window (late notice):
  A cancellation fee applies. Inform the caller before proceeding.

### LATE CANCELLATION FEE

<!-- CLIENT: specify the fee amount and conditions, e.g., "$50 late cancellation fee" or "No fee." -->

Agent script when fee applies:
"Please note that cancellations made less than [CANCELLATION_WINDOW] before the appointment are subject to a [FEE_AMOUNT] fee per {{organization_name}}'s policy."

### NO-SHOW FEE

<!-- CLIENT: specify the no-show fee, e.g., "$75 no-show fee" or "No no-show fee." -->

Agent script for no-show follow-up:
"I see there was a missed appointment on [DATE]. A [NO_SHOW_FEE] no-show fee may be applied per our policy. Would you like to reschedule?"

### RESCHEDULE WINDOW

<!-- CLIENT: specify the reschedule deadline, e.g., "Rescheduling must be requested at least 12 hours before the appointment." -->

- If a reschedule request is received **within** the policy window: proceed normally per `modules/RESCHEDULE_APPOINTMENT.md`.
- If a reschedule request is received **outside** the policy window:
  "I'm sorry — our policy requires reschedule requests to be made at least [RESCHEDULE_WINDOW] before the appointment. I'm not able to process this automatically, but I can escalate it to our team."

### EMERGENCY EXCEPTIONS

If a caller describes an emergency that meets one of the following specific criteria:
- **Documented medical emergency** (hospitalization, acute illness requiring immediate care)
- **Police report or active safety threat** (domestic violence, accident, crime)
- **Natural disaster or declared emergency** affecting the caller's ability to attend

Agent action:
1. Acknowledge empathetically:
   "I'm sorry to hear that. Let me document this and have our team review it right away."
2. Escalate to the human team for an override decision. Do not promise a waiver.
3. Invoke `create_note` with `escalation_flag: "emergency-exception"` and include the stated emergency reason in `issue_summary`.
4. **Escalation priority: URGENT.** Route to Manager or Compliance per the EMERGENCY OVERRIDE section in `modules/ESCALATION.md`.

Note: Vague references to personal difficulty without meeting one of the criteria above do not automatically qualify as an emergency exception. Route to a human for review regardless — do not deny, but also do not promise an override.

### WAIVER AUTHORITY
- The agent is **not authorized** to waive any fees — cancellation or no-show.
- Only human team members may authorize waivers.
- **Canonical fee waiver script** (use this exact wording in all modules):
  "I understand, but I'm not authorized to waive that fee. I can escalate this to our team for review — would that work?"
- After using this script: invoke `transfer_call` if available, or `create_note` with `escalation_flag: "fee-waiver-request"`.
- See `modules/ESCALATION.md` for escalation procedure and authority levels.
- Do not promise a waiver or indicate one is likely.

### RESCHEDULE BEFORE CANCEL

Before confirming any cancellation, the agent must always offer to reschedule:
"Before I cancel — would you like to reschedule for a different time instead?"

### FEES & PENALTIES SUMMARY

<!-- CLIENT: complete this summary table before deploying -->

| Scenario | Fee | Notes |
|----------|-----|-------|
| Cancellation with sufficient notice | $0 | Per cancellation window above |
| Late cancellation | [FEE_AMOUNT] | Per cancellation window above |
| No-show | [FEE_AMOUNT] | Applied after missed appointment |

### GUARDRAILS
- Never confirm a fee amount that is not documented in this policy.
- Never promise a waiver or exception without human authorization.
- Never cancel an appointment the caller has not explicitly confirmed they want cancelled.
- Always log cancellations and reschedules via `create_note` if the confirmation tool is unavailable.
