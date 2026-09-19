# CAP Validation & Business Rules (Node.js)

## Declarative validation
Prefer CDS annotations for reusable constraints:

```cds
entity Products {
  name  : String(100) @mandatory;
  price : Decimal(9,2) @assert.range: [(0), _];
  code  : String @assert.format: '^[A-Z0-9-]+$';
}
```

Use `@mandatory`, `@assert.range`, `@assert.format`, `@assert.enum`, and `@assert.unique` where supported by the target runtime and database.

## Programmatic validation
Implement cross-field and context-dependent rules in `before` handlers:

```js
srv.before('CREATE', 'Orders', req => {
  if (req.data.quantity <= 0) req.reject(400, 'Quantity must be positive')
  if (!req.data.book) req.reject(400, 'Book is required')
})
```

Validate at the service boundary, then re-check rules in transactional code when concurrent changes can invalidate assumptions.

## Status flows
Represent allowed statuses explicitly and validate transitions:
- `New -> Submitted`
- `Submitted -> Cancelled`
- `New -> Cancelled`

Reject transitions not in the state machine. Prefer custom actions such as `submit`, `cancel`, and `approve` over allowing arbitrary status updates.

## Authorization-aware rules
Use `@requires` and `@restrict` for access control, and handlers for rules requiring the current user, tenant, ownership, or database state. Never rely on UI validation alone.

## Error quality
Return clear field-level or operation-level messages, stable error codes, and appropriate status codes. Avoid leaking SQL, credentials, or internal stack traces.

## Checklist
- Rules are expressed declaratively when possible.
- Validation occurs before side effects.
- Cross-entity checks run in a transaction.
- Status transitions are explicit and tested.
- Bulk operations cannot bypass validation.

## References
- https://cap.cloud.sap/docs/guides/providing-services
- https://cap.cloud.sap/docs/cds/cdl
