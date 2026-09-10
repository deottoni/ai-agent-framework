# CHANGELOG

All notable changes to the AI Agent Prompt Framework.

## [1.0.0] — 2026-02-26

### Added
- Initial release: SYSTEM_INSTRUCTIONS, 4 role modules (Sales, Customer Support, Receptionist, Service Request), 6 functional modules
- 7 policy files: Scheduling, Cancellation/Reschedule, Pricing, Payments, Privacy/Security, Voice Style Guide, Abuse/Off-Topic
- 3 tool contract files: TOOL_CONTRACTS, TOOL_USAGE_RULES, TOOL_ERROR_HANDLING
- Pre-deployment checklists in README and PROMPT_LIBRARY_GUIDELINES
- INTENT_ROUTING anti-loop safeguard
- Multi-tool failure cascade in TOOL_ERROR_HANDLING
- escalation_flag enum with 8 routing classifications
- {{placeholder}} variable system for client configuration

## [1.1.0] — 2026-05-30

### Fixed
- Consistent follow_up_required type (boolean) across all modules and policies
- escalation_flag enum casing standardized across all files
- CANCEL_APPOINTMENT now handles multiple_found status from lookup_appointment
- RESCHEDULE_APPOINTMENT respects request_only scheduling mode
- SERVICE_REQUESTS greeting corrected
- SALES_MODULE and VOICE_STYLE_GUIDE prohibited phrase conflict resolved
- PRIVACY_SECURITY_POLICY uses enum values for flag references

### Added
- {{scheduling_mode}} and {{pricing_mode}} added to PROMPT_LIBRARY_GUIDELINES variable list
- VARIABLES.md — complete variable and bracket-value reference
- GETTING_STARTED.md — first-timer deployment guide
- EXAMPLE_DEPLOYMENT.md — fully assembled example prompt

### Changed
- INTERNAL_NOTES.md converted to CHANGELOG.md
- metadata.yml populated
- EXPANSION section updated to reflect existing modules
