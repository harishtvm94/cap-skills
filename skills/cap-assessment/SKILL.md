# CAP Assessment & Quality Review

## Review dimensions
### Modeling
- Entities, keys, types, associations, compositions, aspects, localization, and managed data are intentional.
- Models are simple, flat where practical, and use reuse aspects.

### Services
- APIs are use-case-oriented projections.
- Generic providers are used before custom code.
- Actions, functions, media, status flows, and constraints are designed explicitly.

### Security
- Authentication is configured per environment.
- `@requires` and `@restrict` protect services and instances.
- Tenant isolation, secrets, auditability, and direct API access are tested.

### Implementation
- Handlers use request transactions and clear error contracts.
- External calls have timeouts, retries, idempotency, and observability.
- Events are versioned and reliable.

### Persistence and operations
- Queries are bounded and parameterized.
- Indexes and migrations are reviewed.
- Deployment, backups, monitoring, and rollback are documented.

### Testing
- Unit, service, integration, security, migration, and end-to-end tests cover critical journeys.
- CI runs lint, build, tests, vulnerability scans, and deployment checks.

## Assessment output
Record findings with severity, evidence, affected files, recommendation, owner, and target date. Separate blockers from improvements. Re-run the assessment after remediation.

## Useful commands
```bash
cds lint
cds build
npm test
npm audit
```

## Definition of done
A CAP feature is complete when its model, service contract, authorization, handlers, persistence behavior, tests, deployment configuration, documentation, and operational signals are all reviewed.
