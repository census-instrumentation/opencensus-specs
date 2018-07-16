# View Definition

This document describes the YAML configuration for views.

Implementations should provide a way to load this configuration to define views.
Implementations may provide facilities to:
* watch the configuration file for changes and reload the views
* reload the file explicitly by function call

## Schema

The view definitions appear as an array of [View](#view-object) under a top-level "views" key
and may be contained in a larger configuration file that also contains other OpenCensus-related
configuration.

### Example

```yaml
views:
  - name: grpc.io/client/roundtrip_latency
    description:
      Time between first byte of request sent to last byte of response received, or terminal error.
    measure: grpc.io/client/roundtrip_latency
    columns:
        - tagKey: grpc_client_method
        - tagKey: grpc_client_status
    aggregation:
        type: distribution
        bucketBounds: []
```

Each view definition contains the following fields:

### View object

| Field       | Type                               | Required | Comments                                                                         |
|-------------|------------------------------------|----------|----------------------------------------------------------------------------------|
| name        | string                             | yes      | Name of the view.                                                                |
| description | string                             | no       | Human-readable description. Default is empty.                                    |
| measure     | string                             | no       | Name of measure this view is defined over. Default is the same as the view name. |
| columns     | array of [Column](#column-object)  | no       | List of columns in the view. Default is empty.                                   |
| aggregation | [Aggregation](#aggregation-object) | yes      | Aggregation to apply.                                                            |

### Column object

| Field  | Type   | Required | Comments              |
|--------|--------|----------|-----------------------|
| tagKey | string | yes      | The tag key to select |

### Aggregation object

| Field        | Type             | Required | Comments                                                                                |
|--------------|------------------|----------|-----------------------------------------------------------------------------------------|
| type         | string           | yes      | Type of aggregation. Must be one of the strings: distribution, sum, lastValue, or mean. |
| bucketBounds | array of numbers | no       | Bucket bounds for distribution type aggregation. Required for type: distribution.       |

### Column object

| Field  | Type   | Required | Comments              |
|--------|--------|----------|-----------------------|
| tagKey | string | yes      | The tag key to select |

## Process

When processing a view configuration, implementations should go through the following steps:

1. Syntactic validation
1. Determine added/deleted/changed views
1. Semantic validation
1. Atomic application

If any step fails before atomic application, no changes are made and an error is returned.

### Syntactic validation

Implementations should check that:

1. Required fields are set
1. View names are unique and valid
1. Tag keys per view are unique and valid
1. Bucket bounds are strictly increasing

### Determine added/deleted/changed views

The `views` field is expected to contain the complete set of views that should be defined.
Any existing view not in the configuration file should be deleted.

Views that are unchanged should have their cumulative totals preserved.

### Semantic validation

Optional. Views should be checked that the measures they refer to are defined.

### Atomic application

Atomically swap the current set of views with the new set from the configuration.

> TODO: should unchanged views counters be reset?
