# CAP Events & Messaging (Node.js)

## Event-driven design
Use events to decouple bounded contexts and integrations. Distinguish:
- **Local service events:** internal lifecycle hooks.
- **Domain events:** business facts such as `OrderSubmitted`.
- **Integration events:** contracts published to external systems.
- **Commands:** requests asking another component to perform work.

## Define and emit events
```cds
service OrdersService {
  event OrderSubmitted : { ID : UUID; buyer : String; };
}
```

Publish only after the transaction succeeds. Include stable event names, schema versions, identifiers, timestamps, and correlation IDs. Do not publish secrets or unnecessary personal data.

## Consume events
Register handlers for messaging topics and make consumers idempotent. Store processed event IDs or use business keys to avoid duplicate effects. Handle retries, dead-letter queues, poison messages, and temporary outages.

## Configuration
Use CAP messaging services and environment bindings rather than hard-coded broker URLs or credentials. Keep development mocks separate from production topics.

## Reliability
For database changes plus event publication, use transactional outbox or the platform-supported reliable messaging mechanism. Do not perform non-idempotent external calls inside a database transaction without a recovery strategy.

## Testing
Test publication, payload schemas, duplicate delivery, ordering assumptions, retry behavior, authorization, and backward compatibility. Contract-test producers and consumers.

## References
- https://cap.cloud.sap/docs/guides/messaging
- https://cap.cloud.sap/docs/node.js/messaging
