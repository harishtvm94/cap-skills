# CAP Services & APIs (Node.js)

## Purpose
Design use-case-oriented CAP services, expose domain models safely, and extend generic OData APIs with actions, functions, validations, status flows, and media handling.

## Core topics
- Providing and consuming services
- CDS service definitions and projections
- Generic CRUD, search, pagination, validation, and draft support
- Use-case-oriented APIs and facade design
- Actions, functions, custom handlers, and media resources
- Constraints and status transitions

## Define provided services
```cds
using { my.bookshop as db } from '../db/schema';

@path: '/browse'
service CatalogService {
  @readonly entity Books as projection on db.Books {
    ID, title, author.name as author
  };

  action submitOrder(book : Books:ID, quantity : Integer) returns db.Orders;
}
```

A service declares the entities and operations it provides. Prefer projections over exposing database entities directly. Projections protect the domain model, allow field selection/renaming, and support different APIs for different consumers.

## Services as facades
Use separate services for separate use cases:

```cds
@requires: 'authenticated-user'
service UsersService {
  @readonly entity Orders as projection on db.Orders;
  action cancelOrder(ID : Orders:ID, reason : String);
}

service AdminService {
  entity Books   as projection on db.Books;
  entity Authors as projection on db.Authors;
  entity Orders  as projection on db.Orders;
}
```

Avoid a single service exposing every entity one-to-one. Services are inexpensive; design narrow APIs that reflect business use cases and security boundaries.

## Denormalized views and auto-exposure
Use CQL projections to provide consumer-oriented views, hide internal fields, and flatten navigation data. Use `@cds.autoexpose` for reusable code lists or entities referenced by associations when they must be available to clients.

Associations between projected entities are automatically redirected so navigation remains inside the service boundary.

## Generic providers
CAP generic providers handle standard CRUD, filtering, sorting, paging, search, optimistic concurrency, input conversion, and many validations without custom code. Keep custom handlers focused on business rules that cannot be expressed declaratively.

Useful annotations include `@readonly`, `@insertonly`, `@requires`, `@restrict`, `@assert`, and `@mandatory`.

## Constraints and status flows
Declare constraints in CDS where possible:

```cds
entity Orders {
  status : String enum { New; Submitted; Cancelled; }
  quantity : Integer @assert.range: [1, 999];
}
```

For transitions, validate the current state before changing it in a service handler. Reject invalid transitions with a clear `req.reject(...)` error and keep the state machine explicit and testable.

## Custom actions and functions
- **Actions** change state or perform business operations.
- **Functions** return information without changing state.
- Define parameters and return types in CDS.
- Implement handlers in `srv/*.js`.
- Use `req.data`, `req.params`, `req.user`, and `req.tx`.
- Keep authorization and validation at the service boundary.

```js
module.exports = srv => {
  srv.on('submitOrder', async req => {
    const { book, quantity } = req.data
    if (quantity <= 0) return req.reject(400, 'Quantity must be positive')
    return { ID: cds.utils.uuid(), book, quantity }
  })
}
```

## Media and binary data
For images, documents, and other binary content, model media properties with the appropriate OData media annotations and stream handling. Store small content in the database only when appropriate; use object storage or an external repository for large files. Validate content type, size, authorization, and download access.

## Consuming services
Use destinations and configured credentials for remote services. Prefer typed CDS models and the CAP remote-service API, propagate user context only when required, and handle timeouts, retries, errors, and pagination deliberately.

## Checklist
- Is the service designed for one use case?
- Are database entities exposed through projections?
- Are internal fields excluded?
- Are authorization and constraints declarative where possible?
- Are actions idempotent where appropriate?
- Are custom handlers transactional and tested?
- Are media streams protected and size-limited?

## References
- https://cap.cloud.sap/docs/guides/providing-services
- https://cap.cloud.sap/docs/guides/using-services
- https://cap.cloud.sap/docs/cds/cdl
