# CAP Database and Deployment

Prepare CAP applications for local, CI, and SAP BTP deployment.

## Guidance

- Use SQLite for lightweight local development and SAP HANA for production scenarios requiring it.
- Keep deployment descriptors and service bindings environment-specific.
- Run migrations safely and back up production data.
- Configure health checks, logs, scaling, and graceful shutdown.
- Validate `cds build --production` before deployment.
- Deploy through the project's supported MTA, Kyma, or Cloud Foundry workflow.