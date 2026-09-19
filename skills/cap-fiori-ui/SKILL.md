# CAP Fiori and UI Integration

## Architecture
Keep domain models in `db/`, service projections in `srv/`, and UI annotations in `app/` or separate annotation files. Expose stable OData V4 services designed for the UI use case.

## Fiori annotations
Use CDS annotations such as `@UI.LineItem`, `@UI.SelectionFields`, `@UI.Identification`, `@Common.Label`, `@Common.Text`, `@Common.ValueList`, and `@UI.Facets`.

```cds
annotate CatalogService.Books with @(
  UI.LineItem: [
    { Value: title },
    { Value: author.name },
    { Value: price }
  ],
  UI.SelectionFields: [ title, author_ID ]
);
```

## Metadata-driven behavior
Fiori elements derive list reports, object pages, filters, value helps, draft behavior, and navigation from service metadata and annotations. Ensure annotations target service projections, not only database entities.

## Associations and compositions
Use associations for navigation and compositions for editable document structures. Expose child entities through the service and verify redirected associations, draft support, create/update/delete capabilities, and side effects.

## Localization and formatting
Use localized fields and i18n bundles. Annotate currencies, units, dates, and text arrangements so clients render values correctly. Do not hard-code display labels in handlers.

## UI security
UI visibility is not authorization. Enforce permissions in CDS and server handlers. Hide sensitive fields in projections and test direct OData access.

## Checklist
- Metadata loads without errors.
- Line items, facets, value helps, and labels are correct.
- Draft and composition behavior is tested.
- Messages map clearly to fields.
- Service authorization works independently of the UI.

## References
- https://cap.cloud.sap/docs/advanced/fiori
- https://cap.cloud.sap/docs/advanced/fiori#annotations
