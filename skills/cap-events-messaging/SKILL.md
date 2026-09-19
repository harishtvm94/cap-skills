# CAP Events and Messaging

Use domain events and messaging for decoupled integration.

## Guidance

- Define event names and payloads as versioned contracts.
- Publish events only after successful business transactions where required.
- Make consumers idempotent and resilient to retries.
- Configure SAP Event Mesh or the selected broker through bindings.
- Avoid putting sensitive data into events unnecessarily.
- Test delivery, duplicate messages, failures, and dead-letter handling.