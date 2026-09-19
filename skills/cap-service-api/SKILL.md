# CAP Services and APIs

Define service interfaces in CDS and expose stable projections through OData.

## Guidance

- Expose only fields and entities required by consumers.
- Use projections to protect the domain model.
- Apply service annotations for labels, search, and capabilities.
- Use actions and functions for intentional operations.
- Keep API contracts backward compatible.
- Test `$metadata`, CRUD, filtering, paging, and error responses.