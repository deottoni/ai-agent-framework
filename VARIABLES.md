# VARIABLES.md — Complete Variable Reference

Two types of substitution are required before deployment:

**Type 1: `{{double-curly}}` variables** — replace in SYSTEM_INSTRUCTIONS.md and any file that references them.

**Type 2: `[BRACKET]` client-config values** — embedded in agent scripts. Must also be replaced before deployment. If left as-is, the agent will speak them verbatim to callers.

---

## Type 1: {{double-curly}} Variables

| Variable | Required | Where Used | Description | Example |
|----------|----------|-----------|-------------|---------|
| {{organization_name}} | Yes | All files | Your business name | "Sunrise Plumbing" |
| {{agent_role}} | Yes | SYSTEM_INSTRUCTIONS | The agent's role | "Receptionist" |
| {{industry}} | Recommended | SYSTEM_INSTRUCTIONS, VOICE_STYLE_GUIDE | Your industry for tone calibration | "home services" |
| {{current_date}} | Recommended | SYSTEM_INSTRUCTIONS | Today's date for scheduling context | "2026-05-30" |
| {{contact_data}} | Optional | SYSTEM_INSTRUCTIONS | General contact info block | "Phone: 555-0100, Email: hello@co.com" |
| {{scheduling_mode}} | Yes (if scheduling) | SYSTEM_INSTRUCTIONS | Controls booking behavior | "realtime" or "request_only" or "hybrid" |
| {{pricing_mode}} | Yes (if pricing discussed) | SYSTEM_INSTRUCTIONS | Controls pricing disclosure | "restricted" or "open" or "contextual" |

---

## Type 2: [BRACKET] Client-Config Values

These are embedded in agent scripts. Replace all of them before deploying.

| Value | File(s) | Description | Example |
|-------|---------|-------------|---------|
| [FEE_AMOUNT] | CANCELLATION_RESCHEDULE_POLICY, CANCEL_APPOINTMENT | Late cancellation fee | "$50" |
| [CANCELLATION_WINDOW] | CANCELLATION_RESCHEDULE_POLICY, CANCEL_APPOINTMENT | Notice required to cancel free | "24 hours" |
| [RESCHEDULE_WINDOW] | CANCELLATION_RESCHEDULE_POLICY, RESCHEDULE_APPOINTMENT | Notice required to reschedule free | "12 hours" |
| [NOTICE_PERIOD] | SCHEDULING_POLICY, SCHEDULING_NEW_APPOINTMENT | Minimum advance booking | "2 hours" |
| [NO_SHOW_FEE] | CANCELLATION_RESCHEDULE_POLICY | No-show fee | "$75" |
| [PAYMENT_METHODS] | PAYMENTS_POLICY | Accepted payment types | "Visa, Mastercard, check" |
| [REFUND_WINDOW] | PAYMENTS_POLICY | Refund eligibility window | "within 30 days" |
| [PROCESSING_TIME] | PAYMENTS_POLICY | Refund processing time | "5–7 business days" |
| [PRIVACY_POLICY_URL] | PRIVACY_SECURITY_POLICY | URL to your privacy policy | "https://yourdomain.com/privacy" |
| [CONTACT] | TOOL_ERROR_HANDLING | Fallback contact when all tools fail | "(555) 010-0100" |
| [BOOKING_HOURS] | SCHEDULING_POLICY | Hours bookings are accepted | "Mon–Fri, 8 AM–6 PM ET" |
| [TIMEFRAME] | ESCALATION | Callback turnaround time | "2 business hours" |
| [LOW] / [HIGH] | PRICING_POLICY, SALES_MODULE | Pricing range (contextual mode) | "$150" / "$400" |
| [SERVICE_A/B/C] | SCHEDULING_NEW_APPOINTMENT | Your service menu options | "HVAC Repair", "Tune-Up", "Installation" |

---

## Runtime [BRACKET] Values — Do NOT Replace

These are filled automatically by tool responses during conversations. Leave them as-is.

[DATE], [SERVICE], [TIME], [PHONE], [CONFIRMATION_ID], [ADDRESS], [NAME], [NEW_DATE], [NEW_TIME], [OLD_DATE], [OLD_TIME], [APPOINTMENT_ID], [CANCELLATION_ID], [NOTE_ID], [STATUS], [DESTINATION], [REASON], [PERSON], [MESSAGE], [FIELD], [ISSUE], [TIMELINE], [ALTERNATIVE], [NEXT_AVAILABLE], [OPTION_1], [OPTION_2]
