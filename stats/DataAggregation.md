# Data Aggregation API Overview
The stats library allows users to define views to configure how data are aggregated and broken down.
The core data types used are:
* `Aggregation`: defines the aggregation type (e.g. `Distribution`, `Sum`, `Mean`).
* `View`: defines how individual measurements are broken down by tags (similar to labels in some 
other systems) and aggregated.

## Aggregation
An Aggregation describes how data collected is aggregated.

The library SHOULD provide support for multiple types of Aggregations:
* `Count`: counts the number of measurements recorded.
* `Sum`: indicates that data collected and aggregated with this `Aggregation` will be summed up.
* `Max`: indicates that data collected and aggregated with this `Aggregation` will calculate the 
maximum value recorded.
* `LastValue`: indicates that data collected and aggregated with this `Aggregation` will 
represent the last recorded value. This is useful to support Gauges.
* `Distribution`: indicates that the desired `Aggregation` is a histogram distribution. A
distribution `Aggregation` may contain a histogram of the values in the population. User should
define the bucket boundaries for that histogram.

## View
A View describes how individual measurements are both broken down (by a set of tag keys) and 
aggregated (e.g. into Distributions). A single `Measure` can be used by multiple Views.

A View is defined from the following:
* `name`: a string by which the View will be referred to, e.g. "rpc_latency". Names MUST be unique
within the library.
* `description`: a free format descriptive string, e.g. "RPC latency distribution".
* `Measure`: the Measure to which this view is applied.
* `columns`: an array of tag keys. These values associated with tags of this name form the basis 
by which individual stats will be aggregated (one aggregation per unique tag value). If none are 
provided, then all data is recorded in a single aggregation.
* `aggregation`: `Distribution`, `Count`, `Sum`, `Mean`.

Implementations SHOULD define a View data type, constructed from the parameters above. Views MAY 
have getters for retrieving all of the information used in View definition. Once created, View 
metadata is immutable.

Example in Java
```java
// Define a list of tags to break down view by RPC method.
private static final ArrayList tagKeyListForMethodViews = new ArrayList();
tagKeyListForMethodViews.add(RPC_METHOD_KEY);

// Create latency buckets from 100us to 10s.
private static final BucketBoundaries LATENCY_DISTRIBUTION_BUCKETS =
    new BucketBoundaries(Arrays.asList(0, 0.01, 0.1, 1.0, 10.0, 1000.0, 10000.0));

// Create a distribution with the latency buckets.
private static final Distribution LATENCY_DISTRIBUTION =
    Distribution.create(LATENCY_DISTRIBUTION_BUCKETS);

// Define a view to collect RPC latency stats by RPC method.
private static final View RPC_LATENCY_BY_METHOD_VIEW = new View(
  View.Name.create("rpc_latency_by_method"),
  "Distribution of RPC latency broken down by Method", // description
  RPC_LATENCY, // Measure
  tagKeyListForMethodViews, // aggregation_tags
  LATENCY_DISTRIBUTION);
```

References to Views MAY be obtained from querying of registered Views at runtime. This
functionality enables decoupling the definition of the data aggregation from the exporting
of the aggregated data.

For languages that do not allow private properties/metadata and if they are needed implementations 
MAY define a `ViewDescription` data type which contains all the read-only fields from the `View` 
definition such as: `name`, `description`, `MeasureDescription` and `columns` and `Aggregation`.
