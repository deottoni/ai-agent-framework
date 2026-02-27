# SCHEDULING_POLICY.md

# SCHEDULING POLICY

## SCHEDULING POLICY — AI AGENT

This policy defines the rules and constraints the agent must follow when scheduling appointments.

### SCHEDULING MODES

The scheduling mode controls whether the agent can confirm a booking in-call or must defer to the human team. Exactly one mode must be active per deployment.

<!-- CLIENT: set the active scheduling mode: "realtime", "request_only", or "hybrid" -->

#### `realtime`
The agent invokes `book_appointment` during the call. A booking is confirmed only when the tool returns `status: "confirmed"` and a `confirmation_id`.

- Never say "confirmed," "booked," or "scheduled" before the tool responds with success.
- Script on success: "Your appointment is confirmed for [DATE] at [TIME]. Your confirmation number is [CONFIRMATION_ID]."
- On tool failure: fall back to `create_note` and use the request_only script below. See `tools/TOOL_ERROR_HANDLING.md`.

#### `request_only`
No booking tool is invoked. The agent collects all required fields, reads them back to confirm accuracy, and logs the request via `create_note`. The human team completes the booking manually.

- Do not use the words "booked," "confirmed," or "scheduled."
- Script: "I've noted your request. Our team will reach out to confirm your appointment."
- Invoke `create_note` with all collected fields captured in `issue_summary`.

#### `hybrid`
The agent attempts `book_appointment` first (realtime path). If the tool fails or is unavailable, it automatically falls back to the `request_only` path.

- On tool success: use realtime confirmation script.
- On tool failure or unavailability: "I wasn't able to lock that in right now — I've noted your request and our team will confirm shortly." Then invoke `create_note`.
- Never retry the booking tool without explicit caller re-confirmation. See `tools/TOOL_ERROR_HANDLING.md`.

---

### BOOKING WINDOW

<!-- CLIENT: specify available booking days and hours, e.g., "Appointments are available Monday–Friday, 8:00 AM–6:00 PM" or "24/7 online booking available" -->

The agent must not book appointments outside the approved booking window. If a caller requests a time outside the window:
"We're not able to book at that time. Our available hours are [BOOKING_HOURS]. Would one of those times work for you?"

### ADVANCE NOTICE REQUIREMENT

<!-- CLIENT: specify the minimum advance notice required, e.g., "Appointments must be booked at least 24 hours in advance" -->

If a caller requests a same-day or short-notice appointment that falls within the advance notice window:
"I'm sorry — we require at least [NOTICE_PERIOD] advance notice for new appointments. The earliest I can book you is [EARLIEST_AVAILABLE_DATE]."

### SAME-DAY BOOKING

<!-- CLIENT: specify whether same-day booking is allowed. If allowed, define any conditions or additional steps required. If not allowed, state that clearly. -->

- **If allowed:** Same-day bookings may be accepted subject to real-time availability confirmed by the tool.
- **If not allowed:** "We don't currently offer same-day bookings. I can schedule you for the next available slot — would that work?"

### MAXIMUM CONCURRENT BOOKINGS

<!-- CLIENT: specify whether there is a limit on concurrent active appointments per client, e.g., "Maximum 1 active appointment per client at a time" or "No limit." -->

### CONFLICT HANDLING

- The agent must never double-book a time slot.
- If a requested slot is unavailable, offer the next two available options:
  "That slot isn't available. I have openings on [OPTION_1] and [OPTION_2] — which works better for you?"
- Never leave a caller without an alternative when a slot is unavailable.

### HOLIDAYS AND CLOSURES

<!-- CLIENT: list business holidays and closure dates, or reference an external calendar source (e.g., "refer to your scheduling tool's blocked dates"). If closures are managed dynamically, note that the tool must check availability in real time. -->

The agent must not book appointments on listed holidays or closure dates. If a caller requests a holiday date:
"We're closed on [DATE]. The next available date would be [NEXT_AVAILABLE]. Would you like to book then?"

### SCHEDULING CONFIRMATION

Every confirmed booking must:
1. Be validated by the `book_appointment` tool (confirmation_id returned).
2. Include a verbal summary of service, date, time, and confirmation number.
3. Trigger a confirmation via `send_confirmation` tool if configured.

<!-- CLIENT: specify confirmation channel — SMS, email, or both. -->

### GUARDRAILS
- Never tell a caller a slot is available unless the tool confirms it.
- Never book on behalf of a caller without their explicit verbal confirmation of all key details.
- If the scheduling tool is unavailable, take a note via `create_note` and promise team follow-up. Do not attempt to book manually.
