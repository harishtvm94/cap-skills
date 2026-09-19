# CAP Database & Deployment (Node.js)

## Build targets
Develop with SQLite or in-memory persistence. Deploy production workloads to SAP HANA Cloud or the approved target using CAP build tasks and platform bindings.

```bash
cds build --production
cds deploy --to hana
```

Use project profiles in `package.json` and keep environment-specific settings outside source code.

## HANA readiness
- Use supported CDS types and associations.
- Avoid database-specific SQL unless isolated and documented.
- Check indexes and query plans for high-volume tables.
- Plan migrations for schema changes.
- Test decimal, timestamp, localization, and UUID behavior on the target database.

## Cloud deployment
Typical Cloud Foundry flow:
```bash
mbt build
cf deploy mta_archives/*.mtar
```

Configure destinations, service instances, identity, messaging, and database bindings through platform configuration. Use CI/CD with approvals, logs, health checks, and rollback procedures.

## Production concerns
- Separate dev, test, and production credentials.
- Apply least privilege.
- Monitor health, errors, latency, database capacity, and job queues.
- Back up data and test restoration.
- Use blue/green or rolling deployment strategies when supported.
- Keep migrations backward compatible for running application versions.

## Checklist
- `cds build` succeeds.
- Database artifacts are reviewed.
- Environment bindings are present.
- Smoke tests run after deployment.
- Logs and alerts are configured.
- Rollback and migration plans exist.

## References
- https://cap.cloud.sap/docs/guides/deployment
- https://cap.cloud.sap/docs/guides/databases
