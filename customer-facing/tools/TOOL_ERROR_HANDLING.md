# TOOL_ERROR_HANDLING.md

# TOOL ERROR HANDLING

## TOOL ERROR HANDLING — AI AGENT

This document defines how the agent must handle errors returned by any tool. Errors must be managed calmly and transparently — without exposing technical details to the caller.

### OBJECTIVE
Ensure that tool failures are handled gracefully, callers are never left without a clear path forward, and the human team is always notified when an action could not be completed.

---

### ERROR CATEGORIES

#### 1. Validation Error

**What it means:** One or more required parameters were missing or invalid.
**Likely cause:** Incomplete or incorrectly formatted data collected from the caller.

**Agent action:**
1. Do not expose the raw error message.
2. Identify the missing or invalid field and ask for it again:
   "It looks like I'm missing [FIELD]. Could you provide that?"
3. Re-invoke the tool once the data is corrected.

**User-facing message:**
"Let me just verify one detail — [CLARIFYING QUESTION]."

---

#### 2. Tool Unavailable (HTTP 503 / Service Unavailable)

**What it means:** The tool service is temporarily down or unreachable.

**Agent action:**
1. Do not retry automatically.
2. Inform the caller calmly.
3. Take a note via `create_note` including the intended action and all collected data.
4. Close with a follow-up promise.

**User-facing message:**
"I wasn't able to complete that right now, but I've noted everything and our team will follow up to take care of it."

---

#### 3. Authentication / Permission Error

**What it means:** The agent is not authorized to call this tool, or credentials have expired.

**Agent action:**
1. Do not retry.
2. Escalate to human immediately via `transfer_call` if available, or take a note.
3. Do not expose permission or authentication details to the caller.

**User-facing message:**
"I'm running into a technical issue on my end. Let me connect you with our team to make sure this gets taken care of."

---

#### 4. Timeout

**What it means:** The tool did not respond within the expected time.

**Agent action:**

For **read-only tools** (e.g., `lookup_appointment`, availability checks):
1. Attempt one automatic retry.
2. If the second attempt also times out: inform the caller and take a note.

For **side-effect tools** (`book_appointment`, `cancel_appointment`, `reschedule_appointment`):
1. **Do not retry automatically** — a duplicate action could result in double-booking, double-cancellation, or double-charge.
2. Inform the caller immediately.
3. Ask for re-confirmation before any retry: "It looks like that's taking a moment on our end. Would you like me to try again?"
4. Only retry **once** after explicit re-confirmation.
5. If the retry also fails: take a note via `create_note` and offer team follow-up.

**User-facing message (after failed retry):**
"I wasn't able to complete that — it's taking longer than expected on our end. I'll have our team follow up to confirm everything."

---

#### 5. No Results Found

**What it means:** A lookup returned no matching records (e.g., appointment not found by name/phone).

**Agent action:**
1. Ask for clarification before assuming the record doesn't exist:
   "I wasn't able to find anything under that name and number. Could you double-check the phone number you used?"
2. If a second attempt with corrected data also returns no results:
   - Take a note.
   - Offer team follow-up.

**User-facing message:**
"I wasn't able to find your record — I'll make a note and have our team look into it and reach out."

---

### RETRY POLICY SUMMARY

| Error Type | Auto-Retry? | Max Retries | Requires Re-confirmation? |
|------------|-------------|-------------|--------------------------|
| Validation Error | Yes (after correction) | 1 | No |
| Tool Unavailable (503) | No | 0 | N/A |
| Auth / Permission Error | No | 0 | N/A |
| Timeout — read-only tools | Yes (automatic) | 1 | No |
| Timeout — side-effect tools | Only after caller re-confirmation | 1 | Yes — always |
| No Results Found | Yes (after correction) | 1 | No |
| Delivery Failure (send_confirmation) | No | 0 | N/A |

---

#### 6. Delivery Failure (`send_confirmation`)

**What it means:** The `send_confirmation` tool returned a `delivery_status` of `"failed"`, `"pending"`, `"invalid_recipient"`, or `"opt_out"`.

**Agent action:**
1. Do not retry automatically.
2. Inform the caller without exposing technical details.
3. Read out the confirmation number verbally so the caller has a reference.
4. Invoke `create_note` with `action_taken: "send_confirmation attempted; delivery_status: [STATUS]"`.

**User-facing messages by status:**
- `"failed"` or `"pending"`: "I tried to send you a confirmation, but it may be delayed. Your confirmation number is [CONFIRMATION_ID] — please keep that for your records. Our team will ensure you receive the confirmation."
- `"invalid_recipient"`: "It looks like there may be an issue with the [phone/email] we have on file. Our team will reach out to confirm your details."
- `"opt_out"`: "It looks like you may have opted out of [SMS/email] notifications. Your confirmation number is [CONFIRMATION_ID]. Our team can follow up by phone if needed."

---

### FALLBACK SEQUENCE

If a tool fails and `create_note` is also unavailable:
1. Apologize briefly:
   "I'm sorry — I'm experiencing a technical issue right now."
2. Attempt escalation via `transfer_call` if available.
3. If transfer is also unavailable:
   "I'm not able to complete this right now. Please contact us directly and our team will assist you."
   <!-- CLIENT: insert main contact number or contact page URL here -->

---

### MULTI-TOOL FAILURE CASCADE

If both `create_note` AND `transfer_call` are unavailable simultaneously:

1. Collect the caller's minimum data verbally and read it back:
   "To make sure nothing is lost — your name is [NAME], your number is [PHONE], and your request was [ISSUE SUMMARY]. Is that correct?"
2. Inform the caller:
   "I'm experiencing a technical issue and can't complete this request digitally right now. Please contact us directly at [CONTACT] and reference your name and the issue above — our team will take care of it."
   <!-- CLIENT: insert main contact number or contact channel here -->
3. Do not end the conversation abruptly — give the caller the best available fallback channel.
4. Alert engineering via any available out-of-band monitoring configured in your deployment.
   <!-- CLIENT: define alert/monitoring channel for critical tool failures (e.g., PagerDuty, Slack alert) -->

---

### PROHIBITED ERROR BEHAVIORS
- Never expose raw error codes, HTTP status codes, or stack traces to the caller.
- Never say "error 503" or any technical jargon to the caller.
- Never tell a caller "the system is broken."
- Never leave a caller without a clear next step after a tool failure.
- Never retry a side-effect tool (`book_appointment`, `cancel_appointment`, `reschedule_appointment`) after an error without the caller's explicit re-confirmation.
- Never claim a booking, cancellation, or reschedule was successful unless the tool returned the corresponding success status and ID. A timeout or non-response is not a success.
