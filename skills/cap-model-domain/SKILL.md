# CAP Domain Modeling

Model business domains with CDS entities, types, aspects, compositions, and associations.

## Guidance

- Keep domain models cohesive and explicit.
- Use aspects for reusable concerns such as managed fields.
- Use compositions for ownership and lifecycle containment.
- Use associations for relationships without ownership.
- Add keys, constraints, and annotations deliberately.
- Separate reusable domain definitions from service projections.

Validate models with `cds compile` and test representative scenarios.