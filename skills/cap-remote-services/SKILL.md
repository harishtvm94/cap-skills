# CAP Remote Services

Integrate external OData, REST, and enterprise services through CAP destinations and service definitions.

## Guidance

- Define external services from metadata where possible.
- Configure destinations and credentials outside source code.
- Use `cds.connect.to()` and preserve request context.
- Handle timeouts, retries, pagination, and remote errors explicitly.
- Map external contracts into stable internal APIs.
- Mock remote services in automated tests.