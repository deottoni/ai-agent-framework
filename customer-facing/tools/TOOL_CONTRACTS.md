# TOOL_CONTRACTS.md

# TOOL CONTRACTS

## TOOL CONTRACTS — AI AGENT

This file defines the contracts for all standard tools available to the agent. Each tool's name, purpose, parameters, expected responses, and side effects are documented here.

<!-- CLIENT: before deploying, confirm each tool is available on your platform and adjust parameter names to match your API exactly. Remove any tools that are not available in your environment. -->

---

### `lookup_appointment`

**Purpose:** Look up an existing appointment by caller name and phone number.

**Side effects:** None. Read-only.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Caller's full name |
| `phone` | string | Yes | Phone number associated with the booking |

**Response — Found (single result):**
```json
{
  "status": "found",
  "appointment_id": "[APPOINTMENT_ID]",
  "service_type": "[SERVICE_TYPE]",
  "date": "[DATE]",
  "time": "[TIME]"
}
```

**Response — Found (multiple results):**
```json
{
  "status": "multiple_found",
  "appointments": [
    {
      "appointment_id": "[APPOINTMENT_ID]",
      "service_type": "[SERVICE_TYPE]",
      "date": "[DATE]",
      "time": "[TIME]"
    }
  ]
}
```

**Response — Not Found:**
```json
{
  "status": "not_found"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: confirm that your scheduling system supports appointment lookup by name + phone. If an alternative lookup key is required (e.g., booking reference number), adjust accordingly. -->

---

### `book_appointment`

**Purpose:** Create a new appointment booking.

**Side effects:** Creates a new record in the scheduling system. Irreversible without `cancel_appointment`.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Caller's full name |
| `phone` | string | Yes | Callback phone number |
| `service_type` | string | Yes | Type of service requested |
| `date` | string (YYYY-MM-DD) | Yes | Preferred appointment date |
| `time` | string (HH:MM) | Yes | Preferred appointment time |
| `address` | string | No | Service address (if applicable) |
| `notes` | string | No | Additional caller notes |

**Response — Success:**
```json
{
  "status": "confirmed",
  "confirmation_id": "[CONFIRMATION_ID]"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: confirm tool endpoint, authentication method, and param names match your booking system API -->

---

### `reschedule_appointment`

**Purpose:** Move an existing appointment to a new date and time.

**Side effects:** Modifies an existing booking record. The original slot is released and a new slot is reserved.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointment_id` | string | Conditional | Appointment ID if known |
| `name` | string | Conditional | Caller's full name (if appointment_id not available) |
| `phone` | string | Conditional | Caller's phone (if appointment_id not available) |
| `new_date` | string (YYYY-MM-DD) | Yes | New appointment date |
| `new_time` | string (HH:MM) | Yes | New appointment time |

Note: `appointment_id` OR (`name` + `phone`) must be provided.

**Response — Success:**
```json
{
  "status": "rescheduled",
  "confirmation_id": "[UPDATED_CONFIRMATION_ID]",
  "new_date": "[NEW_DATE]",
  "new_time": "[NEW_TIME]"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: confirm that appointment lookup by name+phone is supported in your scheduling system -->

---

### `cancel_appointment`

**Purpose:** Cancel an existing appointment.

**Side effects:** Marks booking as cancelled in the scheduling system. May trigger a fee depending on policy.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `appointment_id` | string | Conditional | Appointment ID if known |
| `name` | string | Conditional | Caller's full name (if appointment_id not available) |
| `phone` | string | Conditional | Caller's phone (if appointment_id not available) |
| `reason` | string | No | Optional cancellation reason |

Note: `appointment_id` OR (`name` + `phone`) must be provided.

**Response — Success:**
```json
{
  "status": "cancelled",
  "cancellation_id": "[CANCELLATION_ID]",
  "fee_applied": true,
  "fee_amount": "[FEE_AMOUNT_OR_NULL]"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: confirm whether your system returns fee data in the cancellation response, or whether fee calculation is handled by a separate service -->

---

### `create_note`

**Purpose:** Log a structured note for team follow-up. Used after any unresolved request, escalation, tool failure, or abuse event.

**Side effects:** Creates a note record in the CRM or support system.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `caller_name` | string | Yes | Caller's full name |
| `phone` | string | Yes | Callback phone number |
| `issue_summary` | string | Yes | Brief description of the issue (1–3 sentences) |
| `action_taken` | string | No | What the agent did during the call |
| `follow_up_required` | boolean | No | Whether a follow-up is needed (default: true) |
| `timestamp` | string | No | Date and time of the call — auto-populated from `{{current_date}}` |
| `assigned_to` | string | No | Team or individual to assign the note to (see routing logic in `modules/NOTE_TAKING_STANDARD.md`) |
| `escalation_flag` | string | No | Classification flag — one of: `"abuse—tier1"`, `"abuse—tier2"`, `"fee-waiver-request"`, `"emergency-exception"`, `"security-incident"`, `"data-deletion-request"`, `"tool-failure"`, `"transfer-failed"` |

**Response — Success:**
```json
{
  "status": "created",
  "note_id": "[NOTE_ID]"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: map these fields to your CRM or ticketing system's field names. Add any required custom fields (e.g., department, priority) -->

---

### `transfer_call`

**Purpose:** Transfer the caller to a human agent or department.

**Side effects:** Removes the AI agent from the call and routes the caller to the specified destination.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `reason` | string | Yes | Reason for the transfer |
| `destination` | string | Yes | Transfer target: `"human"`, `"billing"`, `"support"`, or a department name |

**Response — Success:**
```json
{
  "status": "transferred",
  "destination": "[DESTINATION]"
}
```

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: define valid destination values for your telephony platform. Replace generic labels ("human", "billing", "support") with your actual team or queue names. -->

---

### `send_confirmation`

**Purpose:** Send a booking, reschedule, or cancellation confirmation to the caller via SMS or email.

**Side effects:** Sends an outbound message to the caller.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `channel` | string | Yes | Delivery channel: `"sms"` or `"email"` |
| `recipient` | string | Yes | Phone number (for SMS) or email address (for email) |
| `message_template` | string | Yes | Template identifier, e.g., `"booking_confirmation"`, `"cancellation_confirmation"` |
| `data` | object | Yes | Dynamic values to populate the template (e.g., `date`, `time`, `confirmation_id`) |

**Response — Success:**
```json
{
  "status": "sent",
  "delivery_status": "delivered | pending | failed | invalid_recipient | opt_out"
}
```

**`delivery_status` values and required agent action:**

| `delivery_status` | Meaning | Agent Action |
|-------------------|---------|--------------|
| `"delivered"` | Message delivered successfully | No action needed |
| `"pending"` | Delivery in progress; outcome unknown | Read out confirmation number verbally; log via `create_note` |
| `"failed"` | Delivery failed | Read out confirmation number verbally; log via `create_note` |
| `"invalid_recipient"` | Phone/email address is invalid or unreachable | Inform caller; log via `create_note` |
| `"opt_out"` | Caller has opted out of this channel | Acknowledge; offer alternative channel or read out confirmation number |

For any non-`"delivered"` status, invoke `create_note` with `action_taken: "send_confirmation attempted; delivery_status: [STATUS]"` to ensure the team is aware.

**Response — Error:**
```json
{
  "status": "error",
  "code": "[ERROR_CODE]",
  "message": "[ERROR_MESSAGE]"
}
```

<!-- CLIENT: define available message templates in your messaging platform. Ensure the `data` field keys match your template variable names exactly. -->

---
