# CAP Remote Services (Node.js)

## Purpose
Consume external OData, REST, and messaging-based services safely through CAP abstractions.

## Model and configure
Import or define the remote service contract in `srv/external/`, configure a destination or service binding, and connect through `cds.connect.to(...)`.

```js
const external = await cds.connect.to('ExternalService')
const result = await external.run(SELECT.from('ExternalService.Entity').limit(20))
```

Never hard-code credentials, tokens, or endpoints in source control.

## Integration patterns
- Use typed external entities and projections.
- Map external contracts to internal domain concepts.
- Keep anti-corruption logic in a dedicated integration module.
- Handle pagination, filtering, throttling, and provider-specific limits.
- Propagate user identity only when explicitly required and supported.

## Resilience
Set timeouts, retry only transient failures, use exponential backoff, and apply circuit-breaker/rate-limit policies where appropriate. Make writes idempotent and reconcile asynchronous failures.

## Transactions and consistency
External calls are not automatically part of the local database transaction. Avoid distributed transaction assumptions. Use status records, outbox events, compensating actions, or scheduled reconciliation.

## Security and observability
Validate TLS and destination configuration, minimize data transfer, redact sensitive payloads, and log correlation IDs, latency, status, and retry counts.

## Testing
Use mocks or service virtualization for unit tests, contract tests for schemas, and controlled integration tests for real destinations.

## References
- https://cap.cloud.sap/docs/guides/using-services
- https://cap.cloud.sap/docs/node.js/using-services
