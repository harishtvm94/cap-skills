# CAP Get Started (Node.js)

## Goal
Create, run, understand, and extend a CAP application using Node.js, CDS, services, and the Bookshop sample.

## Prerequisites
- Node.js LTS and npm
- Git
- A code editor
- Optional: SQLite for local development, SAP HANA and cloud tooling for production

## Create a project
```bash
npm install -g @sap/cds-dk
cds init bookshop
cd bookshop
npm add @sap/cds
cds add samples
npm start
```

Useful commands:
```bash
cds watch
cds serve --with-mocks --in-memory
cds compile . --to edmx
cds build
```

## Project structure
- `db/`: domain model and persistence artifacts
- `srv/`: service definitions and Node.js handlers
- `app/`: UI modules and annotations
- `package.json`: dependencies, scripts, and deployment configuration
- `*.cds`: CDS models
- `*.js`: handlers and application code
- `test/`: automated tests

## Bookshop learning path
1. Define `Books`, `Authors`, and relationships in `db/schema.cds`.
2. Add `CatalogService` in `srv/cat-service.cds`.
3. Expose projections on domain entities.
4. Run with SQLite and inspect OData metadata.
5. Add sample data under `db/data`.
6. Implement a custom action in `srv/*.js`.
7. Add tests and authorization.

## CAP principles
- Model intent in CDS; avoid unnecessary imperative code.
- Keep domain, service, UI, and security concerns separate.
- Prefer generic providers before custom handlers.
- Use projections as API facades.
- Make changes incrementally and verify with `cds watch`, tests, and `cds build`.

## First verification
```bash
curl http://localhost:4004/odata/v4/catalog/$metadata
curl http://localhost:4004/odata/v4/catalog/Books
```

Never commit secrets. Use environment variables, service bindings, or destination configuration for credentials.

## References
- https://cap.cloud.sap/docs/get-started/
- https://cap.cloud.sap/docs/get-started/bookshop
- https://cap.cloud.sap/docs/get-started/concepts
