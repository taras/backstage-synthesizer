# Catalog Plugin Example

This is an example of a hypothetical plugin that has similar functionality to the Catalog plugin but uses GraphQL for fetching data and data-driven rendering. This example has the following composite parts,

## Why YAML and not JSX?

The goal of this architecture is to allow people who're not familiar with React/TypeScript to build performant applications using their company's design system. These people may be SREs, IT people, Product Managers or developers of various experience levels. YAML is the lowest common denominator that everyone is familiar with. YAML is standard in the Cloud Native ecosystem.

## Why GraphQL?

* GraphQL query syntax is a good balance between flexibility and expressiveness.
* GraphQL SDL is concice.
* GraphQL ecosystem provides tools that allow extending the GraphQL SDL with domain-specific features.
* GraphQL ecosystem includes clients that provide an in-memory cache that can be used to synchronize data across multiple components that use the same data.

## API

1. `kind: Plugin` - describes routes provided by this plugin and layouts rendered by each route.
2. `kind: Layout` - describes queries and mutations used by the React components and tree of components.

### kind: Plugin

```yaml
apiVersion: backstage.io/v1alpha1
kind: Plugin
routes:
  services:
    queryParams: # query parameters used by this route - will be merged into params.
      pageSize:
        - type: number
      cursor:
        - type: string
      q:
        - type: string
    layout: # reference to `kind: Layout`
      $file: ./CatalogServicesLayout.yaml
  "services/:namespace/:name":
    params: # will be available on `params` inthe template
      namespace:
        type: string
      name:
        type: string
    # TODO
    # layout:
    #   $file: ./CatalogServiceEntityPage.yaml
```

### kind: Layout

```yaml
apiVersion: backstage.io/v1alpha1
kind: Layout
metadata:
  name: CatalogServices
queries:
  data:
    query:
      $file: ./CatalogServicesQuery.graphql
    variables:
      q: ${{ params.q }}
      pageSize: ${{ params.pageSize }}
      cursor: ${{ params.cursor }}
mutations:
  toggleFavorite:
    $file: ./ToggleFavoriteMutation.graphql
content:
  # tree of components with their mapping
```

