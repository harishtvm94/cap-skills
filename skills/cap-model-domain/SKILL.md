---
name: cap-model-domain
description: Model CAP domains with CDS entities, types, keys, associations, compositions, aspects, annotations, localization, and managed data.
---

# CAP Domain Modeling

Use CDS to capture **intent—what, not how**. Domain models are the source for database persistence models and service definitions, enabling CAP generic providers to supply optimized behavior.

## Learning objectives

- Model entity-relationship domains clearly and concisely.
- Use aspects and annotations to separate concerns.
- Apply CAP common types and aspects such as `cuid` and `managed`.
- Choose appropriate keys, data types, associations, compositions, and projections.
- Model authorization, Fiori annotations, localized fields, and managed data without polluting the core model.

## Domain-modeling principles

### Capture intent, not implementation

Prefer declarative CDS over imperative boilerplate. For example:

```cds
using { cuid, managed } from '@sap/cds/common';

entity Books : cuid, managed {
  title  : localized String;
  descr  : localized String;
  author : Association to Authors;
}
```

The model expresses the business intent while CAP generic providers implement persistence, CRUD, localization, managed fields, and service behavior.

### Entity-relationship modeling

Translate requirements into entities, attributes, and relationships:

```cds
using { cuid } from '@sap/cds/common';

entity Books : cuid {
  title  : String;
  descr  : String;
  genre  : Genre;
  author : Association to Authors;
}

entity Authors : cuid {
  name  : String;
  books : Association to many Books on books.author = $self;
}

type Genre : String enum {
  Mystery;
  Fiction;
  Drama;
}
```

### Domain-driven design

- Keep the core domain the primary focus.
- Collaborate with domain experts.
- Refine domain knowledge iteratively.
- Use CDS as a shared, human-readable modeling language.
- Keep core concepts separate from generic, security, and UI concerns.

## Best practices

### Keep it simple

- Keep models concise, comprehensible, and easy for consumers to use.
- Avoid unnecessary abstraction and complexity.
- Prefer flat models where interoperability is important.
- Prefer concise, readable names.

Prefer:

```cds
entity Contacts {
  isCompany : Boolean;
  company   : String;
  title     : String;
  firstname : String;
  lastname  : String;
}
```

Avoid deeply nested structures unless they add clear domain value.

### Separate concerns with aspects

Keep core entities clean; put security and UI annotations in separate model fragments. Use `extend` and `annotate` for secondary concerns:

```cds
extend Books with {
  internalCode : String;
}

annotate Books with @restrict: [
  { grant: 'READ', to: 'authenticated-user' },
  { grant: 'UPDATE', to: 'content-maintainer' }
];
```

Named aspects can be reused:

```cds
aspect Auditable {
  changedReason : String;
}

extend Books with Auditable;
```

### Naming conventions

- Capitalize entity and type names: `Books`, `Genre`.
- Start element names with lowercase letters: `title`, `author`.
- Pluralize entity names; use singular names for types.
- Prefer concise names: `address`, not `addressInformation`.
- Avoid repeating context: `Authors.name`, not `Authors.authorName`.
- Use `ID` for technical primary keys.
- Avoid unstable project or organization names in namespaces.

## Core CDS concepts

### Namespaces

Namespaces provide unique prefixes without repeating fully qualified names:

```cds
namespace my.company.bookshop;
entity Books {}
entity Authors {}
```

Use namespaces when models are reusable. Reverse-domain naming is a useful convention. Namespaces are prefixes, not special runtime containers.

### Entities and projections

Entities represent domain data and usually become database tables:

```cds
entity Books {
  key ID : UUID;
  title  : String;
}
```

Projections and views expose selected or renamed data:

```cds
entity AvailableBooks as select from Books {
  key ID,
  title,
  author
} where stock > 0;
```

### Primary keys

Use simple, immutable, technical keys. Prefer canonical UUID keys through `cuid`:

```cds
using { cuid } from '@sap/cds/common';
entity Books : cuid { title : String; }
```

Guidance:

- Prefer one simple key over compound keys.
- Prefer UUIDs for distributed systems and integrations.
- Use sequences only when high-volume database-specific optimization justifies them.
- Never interpret or validate UUID formatting beyond uniqueness; treat UUIDs as opaque values.
- Do not use binary data as keys.

### Data types

Built-in CDS types include `UUID`, `Boolean`, date/time types, integer types, `Double`, `Decimal`, `String`, `LargeString`, `Binary`, and `LargeBinary`.

Use common reuse types and aspects from `@sap/cds/common`, such as `Country`, `Currency`, `Language`, `cuid`, `managed`, and `temporal`:

```cds
using { Country, managed } from '@sap/cds/common';
entity Addresses : managed {
  street  : String;
  town    : String;
  country : Country;
}
```

Custom types add semantic meaning or reusable annotations, but avoid excessive indirection:

```cds
type Genre : String enum { Mystery; Fiction; Drama; }
type DayOfWeek : Integer @assert.range: [1, 7];
```

### Associations

Use associations for relationships:

```cds
entity Books {
  key ID : UUID;
  author : Association to Authors;
}

entity Authors {
  key ID    : UUID;
  books     : Association to many Books on books.author = $self;
}
```

Prefer managed to-one associations because CAP derives foreign keys and conditions:

```cds
author : Association to Authors;
```

Use unmanaged associations only when the relationship must be explicitly controlled:

```cds
author   : Association to Authors on author.ID = author_ID;
author_ID : UUID;
```

To-many associations require an `on` condition when targeting persistence. Resolve many-to-many relationships with a link entity:

```cds
entity Projects {
  members : Composition of many Members on members.project = $self;
}
entity Users {
  projects : Composition of many Members on projects.user = $self;
}
entity Members : cuid {
  project : Association to Projects;
  user    : Association to Users;
}
```

### Compositions

Compositions model contained document structures. CAP provides deep insert/update, cascaded delete, and automatic exposure of composition targets:

```cds
entity Orders {
  key ID    : UUID;
  items     : Composition of many OrderItems on items.parent = $self;
}

entity OrderItems {
  key parent   : Association to Orders;
  key pos      : Integer;
  quantity     : Integer;
}
```

Inline composition aspects reduce noise:

```cds
entity Orders {
  key ID : UUID;
  items : Composition of many {
    key pos  : Integer;
    quantity : Integer;
  };
}
```

### Aspects and extensions

Aspects support modular, independently maintained model fragments:

```cds
aspect Trackable {
  trackingCode : String;
}

entity Books : Trackable {
  key ID : UUID;
  title  : String;
}
```

Consumers see the effective model; the separation is transparent.

## Cross-cutting model concerns

### Authorization

Keep authorization annotations in a security model when possible:

```cds
annotate Books with @restrict: [
  { grant: 'READ',   to: 'authenticated-user' },
  { grant: 'CREATE', to: 'content-maintainer' },
  { grant: 'UPDATE', to: 'content-maintainer' },
  { grant: 'DELETE', to: 'admin' }
];
```

Apply restrictions at service/entity boundaries and verify them with authenticated and unauthorized tests.

### Fiori annotations

Keep UI annotations in `app/` or a frontend-specific model file:

```cds
using { my.bookshop as my } from '../db/schema';

annotate my.Books with @(
  UI: {
    Identification: [{ Value: title }],
    SelectionFields: [author_ID, price],
    LineItem: [
      { Value: title },
      { Value: author.name },
      { Value: price }
    ]
  }
);
```

Use `@title`, `@Common`, `@UI`, value lists, semantic keys, text arrangements, and currency annotations as appropriate.

### Localized data

Mark translatable fields with `localized`:

```cds
entity Books {
  key ID : UUID;
  title  : localized String;
  descr  : localized String;
}
```

CAP generates and manages the underlying `.texts` structures and serves localized values. Do not manually duplicate text entities unless a special integration requires it.

### Managed data

Use `managed` or explicit annotations for audit fields:

```cds
using { managed } from '@sap/cds/common';
entity Books : managed {
  title : String;
}
```

Equivalent explicit fields:

```cds
createdAt  : Timestamp @cds.on.insert: $now;
createdBy  : User      @cds.on.insert: $user;
modifiedAt : Timestamp @cds.on.insert: $now @cds.on.update: $now;
modifiedBy : User      @cds.on.insert: $user @cds.on.update: $user;
```

Rules:

- External payloads cannot overwrite managed fields; CAP cleanses them.
- Custom handlers and CSV loading may provide values.
- For UPSERT, update handlers run; insert handlers do not.

Pseudo variables:

- `$now`: current server time in UTC.
- `$user`: authenticated user ID.
- `$user.<attr>`: current user attribute.
- `$uuid`: generated version 4 UUID.

## Implementation checklist

- [ ] Domain requirements are represented as entities and relationships.
- [ ] Core model is concise, flat where practical, and free of UI/security noise.
- [ ] Entity/type naming follows consistent conventions.
- [ ] Entities use immutable canonical keys, preferably `cuid`/UUID.
- [ ] Common reuse types and aspects are used where appropriate.
- [ ] Managed associations are preferred for to-one relationships.
- [ ] Compositions are used for owned document parts.
- [ ] Many-to-many relationships use link entities.
- [ ] Authorization and Fiori annotations are separated into aspects/files.
- [ ] Localized and managed data use CAP built-in support.
- [ ] CDS compilation, persistence generation, and service projections are tested.
