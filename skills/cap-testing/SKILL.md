# CAP Testing

Create automated tests for models, services, handlers, persistence, and authorization.

## Guidance

- Use Node.js test tooling such as Mocha or the project's configured runner.
- Start CAP services in isolated test contexts.
- Use fixtures and deterministic database state.
- Test both OData requests and direct service calls where useful.
- Cover happy paths, validation failures, authorization failures, and transaction rollback.
- Run `npm test`, linting, and `cds compile` in CI.