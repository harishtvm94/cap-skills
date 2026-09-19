# CAP Persistence and Queries

Use CAP persistence services and CQN for database-independent data access.

## Guidance

- Use `cds-plugin-sqlite` for local development where appropriate.
- Use `cds.connect.to('db')` for explicit database access.
- Prefer `SELECT`, `INSERT`, `UPDATE`, and `DELETE` from `@sap/cds`.
- Use parameterized CQN rather than string-built SQL.
- Keep persistence logic inside transactions.
- Design indexes and constraints for production workloads.

Check generated SQL and test against the target database dialect.