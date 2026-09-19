---
name: cap-events-messaging
description: Design and implement CAP events, messaging, transactional event queues, scheduling, retries, and operational diagnostics in Node.js.
---

# CAP Events, Messaging, and Event Queues

## Transactional Event Queues

Use transactional event queues when an event must be committed or discarded with the surrounding transaction and reliably retried across restarts and application instances. In Node.js, wrap a service with `cds.queued()` or enable queueing through configuration. Persistent queueing is enabled by default for queued services.

Do not confuse queues with `cds.spawn()`:

- `cds.spawn()` runs an in-memory detached continuation in a fresh root transaction.
- A crash can lose spawned work, and each application instance runs its own copy.
- Use it for idempotent, disposable in-process work such as periodic cache refreshes.
- Use event queues for transactional integration, persistence, retries, and multi-instance processing.

## Queueing a Service

```js
const srv = await cds.connect.to('yourService')
const queued = cds.queued(srv)

await queued.emit('someEvent', { some: 'message' })
await queued.send('someEvent', { some: 'message' })
```

`cds.queued(srv)` returns a queued proxy. Calls to `emit()`, `send()`, and `run()` are persisted in the current transaction and dispatched after commit. Always `await` the call: the database write must complete inside the transaction.

`cds.outboxed(srv)` is a backward-compatible synonym for `cds.queued(srv)`.

Recover the original synchronous service when required:

```js
const synchronous = cds.unqueued(queued)
```

`cds.unboxed(srv)` is the backward-compatible synonym.

## Configuration-Based Queueing

Enable queueing on an outbound service in `package.json`:

```json
{
  "cds": {
    "requires": {
      "yourService": {
        "kind": "odata",
        "outboxed": true
      }
    }
  }
}
```

Some services, including `cds.MessagingService` and `cds.AuditLogService`, are outboxed by default.

## Scheduling Singleton Tasks

`srv.schedule()` behaves like queued `send()`, so scheduling occurs inside the current transaction and dispatches after commit. It upserts one task identified by its event name rather than inserting a new entry for every call:

```js
await srv.schedule('someEvent', { some: 'message' })
await srv.schedule('someEvent', { some: 'message' }).after('1h')
await srv.schedule('someEvent', { some: 'message' }).every('10m')
await srv.schedule('someEvent', { some: 'message' }).every('*/10 * * * *')

await srv.unschedule('someEvent')
```

`.after()` accepts milliseconds or values such as `1s`, `10m`, and `1h`. `.every()` accepts the same durations or a five-field Node.js cron expression. Java cron expressions commonly contain six fields including seconds; do not copy them between stacks without adapting them.

A later `schedule()` call with the same task name overwrites the existing schedule. Use `.as(name)` for independent tasks using the same event:

```js
await srv.schedule('replicate', { entity: 'Airports' })
  .every('10m').as('replicate-airports')
await srv.schedule('replicate', { entity: 'Airlines' })
  .every('1h').as('replicate-airlines')

await srv.unschedule('replicate-airports')
await srv.unschedule('replicate-airlines')
```

## Processing Callbacks (Node.js)

Register callbacks for individual events after successful processing or after all retries are exhausted:

```js
srv.after('someEvent/#succeeded', (data, req) => {
  console.log('Processed:', data)
})

srv.after('someEvent/#failed', (error, req) => {
  console.error('Dead letter:', error)
})
```

The `*` wildcard does not match `#succeeded` or `#failed`; register the specific callback event.

## Manual Processing

The background runner automatically processes pending messages. `cds.flush()` is useful when an operator revives a dead-letter entry and wants immediate processing:

```js
await cds.flush()
```

The promise resolves when currently processable messages have been dispatched and the runner is idle. Handler failures do not reject the promise; failed messages are rescheduled for a later retry.

## Configuration and Disabling Queues

Queue messages are stored in `cds.outbox.Messages` within the current transaction. Tune queue behavior through `cds.requires.queue`:

```json
{
  "cds": {
    "requires": {
      "queue": {
        "maxAttempts": 10,
        "timeout": "1h"
      }
    }
  }
}
```

The queue configuration is resolved automatically by `cds.env`; specify it only when tuning. Review `legacyLocking` requirements carefully during rolling upgrades across CAP versions.

Disable queues globally:

```json
{ "cds": { "requires": { "queue": false } } }
```

Disable queueing for one service:

```json
{
  "cds": {
    "requires": {
      "messaging": {
        "kind": "enterprise-messaging",
        "outboxed": false
      }
    }
  }
}
```

## Troubleshooting

Inspect queued and failed messages directly:

```js
const db = await cds.connect.to('db')
const messages = await SELECT.from('cds.outbox.Messages')
  .columns('ID', 'target', 'status', 'attempts', 'lastAttemptTimestamp', 'lastError')
  .orderBy('timestamp desc')
```

Delete stuck entries deliberately and preferably through an administrative operation:

```js
await DELETE.from('cds.outbox.Messages')
```

If `cds.outbox.Messages` is missing, check custom database model configuration:

```json
{
  "cds": {
    "requires": {
      "db": {
        "model": ["db", "srv", "@sap/cds/srv/outbox"]
      }
    }
  }
}
```

Projects using the standard `db`, `srv`, and `app` layout generally need no extra model entry. Older CAP projects with custom build tasks may also need `@sap/cds/srv/outbox` in the build task's `options.model`.

## Operational Guidance

- Make queued handlers idempotent because retries can repeat delivery.
- Keep event payloads stable and versionable; avoid exposing internal database structures unnecessarily.
- Use explicit timeouts and retry limits appropriate to the downstream service.
- Monitor pending, retrying, and dead-letter counts, along with `lastError` and attempt timestamps.
- Treat deletion and dead-letter revival as controlled administrative actions.
- Keep queue writes and business changes in the same transaction when atomicity is required.
- Test commit, rollback, restart recovery, duplicate delivery, retry exhaustion, and multi-instance processing.
