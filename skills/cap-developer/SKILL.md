---
name: cap-developer
description: Central routing skill for SAP Cloud Application Programming Model (CAP) development with Node.js. Use this skill to select the right CAP skill, understand the cds facade, bootstrap lifecycle, runtime APIs, and cross-cutting development practices.
---

# CAP Developer Router — Node.js

Use this skill as the entry point for CAP Node.js work. Identify the concern first, then use the focused skill:

- Project setup: `cap-get-started`
- CDS domain modeling: `cap-model-domain`
- Persistence and CQN: `cap-persistence-query`
- Services and OData APIs: `cap-service-api`
- Handlers and transactions: `cap-handlers-transactions`
- Validation and rules: `cap-validation`
- Testing: `cap-testing`
- Security: `cap-security`
- Fiori/UI integration: `cap-fiori-ui`
- Events and messaging: `cap-events-messaging`
- Remote services: `cap-remote-services`
- Database/deployment: `cap-database-deployment`
- Upgrades/migrations: `cap-upgrade`
- Quality review: `cap-assessment`

## The `cds` Facade

Import the CAP facade once and use it as the supported entry point to Node.js APIs:

```js
const cds = require('@sap/cds')
const csn = cds.compile(`entity Foo { key ID : UUID }`)
```

Use `cds repl` to explore APIs interactively:

```text
[dev] cds repl
> cds.compile(`entity Foo { key ID : UUID }`)
```

Do not import internal files below `@sap/cds/`; internal paths are unsupported and may change:

```js
// Good
const { Request } = require('@sap/cds')

// Bad
const Request = require('@sap/cds/lib/.../Request')
```

### Common facade APIs

The facade exposes lazy-loaded references to CAP submodules. Frequently used APIs include:

- Model tooling: `cds.model`, `cds.resolve()`, `cds.load()`, `cds.parse()`, `cds.compile()`, `cds.linked()`
- Server/runtime: `cds.server`, `cds.serve()`, `cds.services`, `cds.middlewares`, `cds.protocols`
- Connectivity: `cds.connect()`, `cds.tx()`, `cds.db`, `cds.ql`
- Configuration and context: `cds.env`, `cds.requires`, `cds.context`, `cds.i18n`
- Security and utilities: `cds.auth`, `cds.test`, `cds.log()`, `cds.utils`

Core classes include `cds.Service`, `cds.ApplicationService`, `cds.RemoteService`, `cds.MessagingService`, `cds.DatabaseService`, `cds.SQLService`, `cds.EventContext`, `cds.Event`, `cds.Request`, and `cds.User`.

Linked CSN types and classes are available through `cds.builtin.types` and `cds.linked.classes`; convenience constructors include `cds.entity`, `cds.event`, `cds.type`, `cds.array`, `cds.struct`, `cds.service`, `cds.Association`, and `cds.Composition`.

### Runtime properties

- `cds.version`: loaded `@sap/cds` version; use sparingly for compatibility branches.
- `cds.home`: installation directory of the loaded CAP package.
- `cds.root`: absolute project root used for CAP file access; normally `process.cwd()`.
- `cds.cli`: parsed command and arguments (`serve`, `build`, `compile`, `deploy`, etc.).
- `cds.model`: effective compiled model loaded during bootstrapping.
- `cds.entities`: iterable dictionary of linked entity definitions; access entities directly or by namespace.
- `cds.env`: effective configuration from package configuration, service bindings, and environment variables.
- `cds.requires`: convenient overlay for configured services, including lookup aliases.
- `cds.services`: cache and iterable dictionary of connected/served `cds.Service` instances.
- `cds.app`: Express application created during bootstrapping.
- `cds.db`: shortcut to the primary database service (`cds.services.db`).
- `cds.context`: current tenant, user, locale, and root transaction context.

Example:

```js
const path = require('node:path')
const cds = require('@sap/cds')

const packageJson = require(path.join(cds.root, 'package.json'))
const { Books } = cds.entities
const books = await SELECT.from(Books) // equivalent to cds.db.run(SELECT.from(Books))
```

CAP Node.js supports Express 4 and Express 5 in current releases. Verify the effective version with `npm ls express`; avoid pinning a direct Express dependency unless custom middleware requires it.

## Errors and lifecycle

Use `cds.error()` for consistent CAP errors:

```js
const error = new cds.error('Invalid request', { code: 'INVALID_REQUEST' })
// Or throw immediately:
value || cds.error.expected`Expected value, got: ${value}`
```

Preserve the original error when augmenting it, and do not catch unexpected programming errors merely to continue execution.

The facade is an EventEmitter. Register lifecycle handlers with `cds.on()` for events such as `bootstrap`, `served`, `listening`, and `shutdown`:

```js
cds.on('bootstrap', app => {
  // register middleware or routes
})

cds.on('shutdown', async () => {
  // release application resources
})
```

`cds.exit()` performs graceful shutdown by emitting `shutdown` handlers before stopping the server. Event handlers are synchronous by default; `served` and `shutdown` support asynchronous handlers.

## Practical rules

1. Prefer the facade and public APIs over internal modules.
2. Use `cds.context` and `cds.tx()` for request-scoped work and transactions.
3. Use `cds.env`/`cds.requires` instead of reading service bindings directly.
4. Use `cds.db` or an injected service rather than creating unmanaged database connections.
5. Keep version-specific branches short and remove them when supported versions converge.
6. Route detailed implementation work to the focused skill files listed above.
