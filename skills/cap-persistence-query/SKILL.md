# CAP Persistence & Queries (Node.js)

## Scope
Model persistence, use SQLite locally and SAP HANA in production, and access data safely with CQL/CQN and the CAP query APIs.

## Persistence model
CDS entities become database tables or views. Use `cuid`, `managed`, reuse types, associations, compositions, and projections to express intent. Keep persistence models stable and service projections consumer-specific.

## Local database
```bash
npm add -D @cap-js/sqlite
cds deploy --to sqlite:db.sqlite
```

Use in-memory persistence for fast tests and SQLite for realistic local development. Do not treat local SQLite behavior as a complete substitute for HANA semantics.

## CQL and CQN
```js
const { SELECT, INSERT, UPDATE, DELETE } = cds.ql
const books = await SELECT.from('sap.capire.bookshop.Books')
  .columns('ID', 'title')
  .where({ genre_ID: '...' })
  .orderBy('title')
  .limit(20)
```

Use parameterized query builders rather than concatenating user input. Prefer selecting only required columns and applying paging at the database.

## Transactions
Use `cds.tx(req)` or `req.tx` inside request handlers. Put related writes in one transaction and never manually commit or roll back a request-managed transaction.

```js
await cds.tx(req).run([
  UPDATE(Books).set({ stock: { '-=': 1 } }).where({ ID }),
  INSERT.into(OrderItems).entries(item)
])
```

## Associations and compositions
Use managed to-one associations where possible. Use compositions for document ownership and deep insert/update. Model to-many associations with valid `on` conditions. Understand generated foreign keys and redirected associations in services.

## Query concerns
- Validate filters and business authorization.
- Use `req.query` only after applying policy checks.
- Avoid N+1 queries; use joins, expands, or batched reads.
- Add indexes based on measured query plans.
- Handle `null`, decimals, dates, and UUIDs consistently.
- Use optimistic locking where concurrent updates matter.

## Database deployment
Build database artifacts with `cds build --production`. Configure HANA through project profiles and deploy using the approved CI/CD or Cloud Foundry workflow. Keep schema changes backward compatible during rolling deployments.

## Checklist
- Queries are parameterized and bounded.
- Transactions cover all related writes.
- Authorization is enforced before reads and writes.
- Pagination and ordering are deterministic.
- Production database behavior is tested.

## References
- https://cap.cloud.sap/docs/guides/databases
- https://cap.cloud.sap/docs/node.js/cds-ql
