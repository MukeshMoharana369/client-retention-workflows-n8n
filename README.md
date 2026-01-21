This repository contains a 4-workflow client check-in and retention system built using n8n.

The system is designed to prevent silent client churn by ensuring that:

every active client is regularly checked in,

risk signals from responses are detected,

non-responding clients are explicitly flagged,

and the coach receives a clear, human-readable summary each cycle.

This system does not coach clients, motivate them, or make decisions on their behalf.
It exists purely to provide visibility, consistency, and control.

Problem This Solves

Most client churn does not happen after complaints.
It happens when clients slowly stop responding and disappear unnoticed.

Manual follow-ups rely on memory and attention, which does not scale.

This system turns:

responses into structured signals, and

silence into a detectable event.

System Architecture

The system is intentionally split into four independent workflows, each with a single responsibility.

Workflow 1 — Check-In Sender

Sends scheduled check-in forms to eligible clients.

Runs on a fixed schedule

Targets only active, paid clients with check-ins enabled

Does not analyze or store responses

Reads: entities
Writes: none

Workflow 2 — Check-In Receiver & Risk Evaluation

Processes submitted check-in forms and evaluates risk.

Triggered by form submission

Applies deterministic rules (energy, progress, pain, blockers)

Flags responses as at-risk or healthy

Stores structured response data

Reads: form submission
Writes: checkin_responses

Workflow 3 — Silence / Ghost Detection

Detects clients who were expected to submit a check-in but did not.

Compares expected clients against submitted responses for the cycle

Flags missing clients as silent

Records silence as a first-class event

Reads: entities, checkin_responses
Writes: silence_flags

Workflow 4 — Cycle Summary & Human Notification

Aggregates the entire cycle into a single summary for human review.

Collects response data and silence flags

Categorizes clients as healthy, at-risk, or silent

Sends one concise summary notification

Does not write or modify any data

Reads: checkin_responses, silence_flags
Writes: none

Data Model

The system uses three Google Sheets, each with a clearly defined role.

entities

Defines who is expected to participate in check-ins.

Typical fields:

entity_id

name

email

status

payment_status

checkin_enabled

checkin_responses

Stores submitted check-in data and evaluated risk.

Typical fields:

timestamp

entity_id

cycle_id

risk_flag

risk_reasons

response-related fields

silence_flags

Stores clients who failed to submit a check-in for a cycle.

Typical fields:

entity_id

cycle_id

status (SILENT)

reason

detected_at

Design Principles

One workflow = one responsibility

Silence is treated as a signal, not an absence of data

No AI decision-making or coaching logic

Human judgment remains final

Readable, auditable, and debuggable workflows

Intended Use Cases

Coaches managing 10+ active clients

Consultants running recurring programs

Service businesses where retention depends on consistent engagement

Not designed for:

lead nurturing

marketing automation

high-touch concierge coaching

Disclaimer

This system provides visibility, not outcomes.

It does not guarantee client results or retention.
It ensures that no client is ignored by the system.

Author

Built as a practical automation system using n8n, focused on real operational problems rather than demos or templates.
