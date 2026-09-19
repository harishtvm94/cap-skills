# CAP Security

Secure CAP services with authenticated identities, role-based authorization, and least privilege.

## Guidance

- Configure the intended identity provider for each environment.
- Declare required roles with `@requires` and `@restrict`.
- Never trust client-supplied user or tenant values.
- Protect administrative actions and sensitive fields.
- Keep secrets in platform bindings or secret stores, not source control.
- Test anonymous, authenticated, unauthorized, and cross-tenant access.