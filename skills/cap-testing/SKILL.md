# CAP Testing (Node.js)

## Test layers
- **Model tests:** CDS compilation, projections, annotations, and generated metadata.
- **Unit tests:** pure functions and individual handlers.
- **Service tests:** HTTP/OData behavior, authorization, validation, and transactions.
- **Integration tests:** database, remote services, messaging, and deployment bindings.
- **End-to-end tests:** UI and complete business journeys.

## Test setup
Use Node.js test tooling such as `@cap-js/cds-test` and a disposable SQLite or in-memory database. Keep tests deterministic and independent.

```js
const cds = require('@sap/cds')
const { GET, POST } = cds.test('.with(srv => srv)')

it('reads books', async () => {
  const { data } = await GET('/odata/v4/catalog/Books')
  expect(data.value).toBeDefined()
})
```

## What to verify
- CRUD and projections
- Actions and functions
- Mandatory fields and constraints
- Authorization and tenant isolation
- Status transitions
- Rollback on failure
- Pagination, filtering, sorting, and search
- Localized and managed data
- Remote service failure and retry behavior
- Event publication and consumption

## Test data
Use minimal fixtures, reset state between tests, and avoid production data. Assert business outcomes rather than implementation details. Use fake clocks and controlled UUIDs only where necessary.

## Quality gates
```bash
npm test
cds lint
cds build
```

Run tests in CI with dependency and database versions matching supported production versions. Include coverage for critical rules, but prioritize meaningful scenarios over a coverage percentage.

## References
- https://cap.cloud.sap/docs/node.js/cds-test
- https://cap.cloud.sap/docs/guides/ testing
