# CAP Upgrades and Migration

Upgrade CAP runtimes and dependencies deliberately.

## Workflow

1. Review CAP release notes and compatibility guidance.
2. Update dependencies in a branch.
3. Run compile, lint, unit, integration, and deployment checks.
4. Review deprecated APIs and generated artifacts.
5. Test database migrations and rollback plans.
6. Document behavior changes and release the upgrade incrementally.

Avoid unreviewed major-version upgrades in production.