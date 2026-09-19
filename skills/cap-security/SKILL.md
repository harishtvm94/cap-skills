# CAP Security (Node.js)

## Authentication and authorization
Configure the platform identity provider and CAP authentication strategy for each environment. Use `@requires` for coarse service/operation protection and `@restrict` for fine-grained authorization.

```cds
@requires: 'authenticated-user'
service AdminService {
  @restrict: [
    { grant: 'READ', to: 'viewer' },
    { grant: ['CREATE','UPDATE'], to: 'editor' }
  ]
  entity Books as projection on db.Books;
}
```

## Instance-based restrictions
Use predicates such as `where: 'createdBy = $user'` or tenant-aware conditions to limit records. Verify restrictions on every exposed path, including navigations, actions, functions, batch requests, and custom handlers.

## User context
Use `req.user`, roles, and trusted attributes supplied by the authentication middleware. Never trust user IDs or roles supplied in request payloads. Keep authorization decisions server-side.

## Secure design
- Store secrets in bindings, destinations, or secret managers.
- Do not log tokens, passwords, or personal data unnecessarily.
- Validate input and bound file uploads.
- Use HTTPS and secure cookie/session settings.
- Apply tenant isolation consistently.
- Use least-privilege technical users.
- Keep dependencies patched.

## Custom handlers
Generic authorization must not be bypassed by direct database access. For privileged operations, explicitly check roles and ownership before performing writes. Add audit logging for sensitive actions.

## Security testing
Test anonymous, authenticated, insufficient-role, cross-tenant, owner, batch, navigation, and action scenarios. Review generated service metadata and exposed entities.

## References
- https://cap.cloud.sap/docs/guides/security/
- https://cap.cloud.sap/docs/node.js/authentication
