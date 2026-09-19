# CAP Upgrades & Migration (Node.js)

## Upgrade strategy
Track `@sap/cds`, adapters, database drivers, UI libraries, and build tooling as a compatible set. Read CAP release notes and migration guides before upgrading.

## Safe workflow
1. Create a branch and record current versions.
2. Update dependencies deliberately.
3. Run `npm test`, `cds lint`, and `cds build`.
4. Review compiler warnings, generated metadata, and database diffs.
5. Run service, security, messaging, and integration tests.
6. Deploy to a staging environment.
7. Validate migrations and rollback behavior.
8. Release with monitoring and a rollback plan.

## Breaking changes
Review deprecated CDS syntax, changed runtime behavior, OData metadata, authentication defaults, database adapters, messaging APIs, and Node.js support. Do not suppress warnings without understanding their impact.

## Schema migration
Make changes additive first where rolling deployments are possible: add columns, deploy compatible code, backfill data, then remove obsolete fields in a later release. Back up production data and test migration duration.

## Dependency hygiene
Use lockfiles, automated vulnerability scanning, reproducible builds, and a documented support policy. Do not upgrade production dependencies directly without CI validation.

## References
- https://cap.cloud.sap/docs/releases/
- https://cap.cloud.sap/docs/guides/upgrade
