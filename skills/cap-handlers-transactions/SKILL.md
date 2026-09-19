# CAP Handlers & Transactions (Node.js)

## Handler lifecycle
CAP handlers can run before, on, or after an event:

```js
module.exports = srv => {
  srv.before('CREATE', 'Orders', validateOrder)
  srv.on('submitOrder', submitOrder)
  srv.after('READ', 'Books', enrichBooks)
}
```

- `before`: validate, normalize, and authorize.
- `on`: implement custom operations or replace default processing.
- `after`: enrich results without changing persistence semantics.

Use the narrowest event and entity target possible.

## Request context
Use `req.data`, `req.params`, `req.query`, `req.user`, `req.locale`, and `req.tx`. Report errors with `req.reject(status, message)` or warnings with `req.warn(...)`. Never expose stack traces or sensitive data to clients.

## Transactions
Request handlers run in a transaction context. Use `req.tx.run(...)` for database operations belonging to the request. For background work, create an explicit transaction and define retry/error behavior.

```js
srv.on('CREATE', 'Orders', async req => {
  return req.tx.run(INSERT.into(Orders).entries(req.data))
})
```

Keep transaction boundaries short. Avoid network calls inside database transactions when possible. Ensure external side effects are idempotent and use an outbox/event pattern where consistency matters.

## Generic vs custom code
Let generic providers handle CRUD. Add custom code only for domain logic, integration, derived values, or operations that cannot be modeled declaratively. Do not duplicate generic validations or bypass authorization.

## Error handling
Use stable error codes, actionable messages, and correct HTTP status mappings. Distinguish client validation errors from conflicts, authentication failures, authorization failures, and infrastructure errors.

## Performance and reliability
Avoid synchronous CPU-heavy work, unbounded loops, and N+1 queries. Use async APIs. Add timeouts to remote calls, structured logging, correlation IDs, and metrics.

## Testing
Test happy paths, invalid transitions, authorization, rollback behavior, duplicate requests, and concurrent updates using isolated databases.

## References
- https://cap.cloud.sap/docs/node.js/core-services
- https://cap.cloud.sap/docs/node.js/events
