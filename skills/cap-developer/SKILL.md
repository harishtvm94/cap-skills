# CAP Developer

Use this skill as the central router for Node.js SAP Cloud Application Programming Model development.

## Route by task

- Project setup or local execution → `cap-get-started`
- CDS entities and relationships → `cap-model-domain`
- Database access and CQN → `cap-persistence-query`
- OData services and projections → `cap-service-api`
- Custom behavior and transactions → `cap-handlers-transactions`
- Validation and invariants → `cap-validation`
- Automated tests → `cap-testing`
- Identity and authorization → `cap-security`
- Fiori elements and UI → `cap-fiori-ui`
- Events and brokers → `cap-events-messaging`
- External APIs → `cap-remote-services`
- HANA, Cloud Foundry, Kyma, or deployment → `cap-database-deployment`
- Version upgrades → `cap-upgrade`
- Readiness review → `cap-assessment`

## Default engineering workflow

1. Clarify the business capability and consumers.
2. Model the domain in CDS.
3. Expose least-privilege service projections.
4. Rely on generic providers before adding custom handlers.
5. Add handlers only for behavior the framework cannot provide.
6. Keep database work inside request transactions.
7. Add authorization and validation before integration.
8. Test locally, in CI, and against production-like services.
9. Build and deploy with environment-managed credentials.
10. Assess the result against the quality checklist.

Prefer official CAP conventions, small changes, explicit contracts, secure defaults, and tests that reproduce production behavior.