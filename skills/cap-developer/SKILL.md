# CAP Developer Router (Node.js)

Use this router to select the focused CAP skill before making changes.

## Workflow
1. Understand the business use case and bounded context.
2. Model domain data in CDS.
3. Define a narrow service projection and API.
4. Add declarative authorization and validation.
5. Use generic providers first.
6. Add transactional handlers only for domain-specific behavior.
7. Add tests, events/integrations, UI annotations, and deployment configuration as needed.
8. Run assessment and quality gates.

## Skill map
- Project setup and Bookshop: `cap-get-started`
- Domain modeling: `cap-model-domain`
- Persistence and CQN: `cap-persistence-query`
- Services and OData APIs: `cap-service-api`
- Handlers and transactions: `cap-handlers-transactions`
- Validation and status flows: `cap-validation`
- Testing: `cap-testing`
- Authentication and authorization: `cap-security`
- Fiori/UI annotations: `cap-fiori-ui`
- Events and messaging: `cap-events-messaging`
- Remote service consumption: `cap-remote-services`
- Database and deployment: `cap-database-deployment`
- Upgrades and migrations: `cap-upgrade`
- Completeness review: `cap-assessment`

## End-to-end checklist
- [ ] CDS model captures intent and uses stable keys.
- [ ] Service is a use-case-oriented facade.
- [ ] Authorization is enforced server-side.
- [ ] Constraints and status transitions are validated.
- [ ] Database operations are parameterized and transactional.
- [ ] External calls and events are resilient and idempotent.
- [ ] Tests cover business, security, and failure paths.
- [ ] Deployment, observability, migration, and rollback are ready.

## References
- https://cap.cloud.sap/docs/get-started/
- https://cap.cloud.sap/docs/guides/providing-services
- https://cap.cloud.sap/docs/node.js/
