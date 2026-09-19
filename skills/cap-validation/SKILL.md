# CAP Validation and Business Rules

Enforce data quality close to the service boundary while keeping reusable invariants in the model.

## Guidance

- Use CDS annotations for declarative constraints.
- Use `before` handlers for validation before persistence.
- Return clear, actionable messages with suitable HTTP status codes.
- Validate authorization separately from business rules.
- Prevent partial writes with transactions.
- Add tests for valid, invalid, boundary, and duplicate cases.