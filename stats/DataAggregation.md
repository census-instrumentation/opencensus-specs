# Data Aggregation API Overview
The stats library MUST allow users to define measurements how data are aggregated and broken down.
The core data types used are:
* `Aggregation`: defines the aggregation type (e.g. `Distribution`, `Sum`, `Mean`).
* `View`: defines how individual measurements are broken down and aggregated.

## Aggregation
An Aggregation describes how data collected is aggregated.

The library MUST provide support for multiple types of Aggregations:
* `Count`: indicates that data collected and aggregated with this `Aggregation` type will be turned
into a count value.
* `Sum`: indicates that data collected and aggregated with this `Aggregation` will be summed up.
* `Mean`: indicates that data collected and aggregated with this `Aggregation` will calculate the
mean value.
* `Distribution`: indicates that the desired `Aggregation` is a histogram distribution. A
distribution `Aggregation` may contain a histogram of the values in the population. User MUST
define the bucket boundaries for that histogram.

## View
A View describes how individual measurements are both broken down (by a set of tag keys, which
form the basis for a Metric labels.) and aggregated (e.g. into Distributions). A single Measure
can be used by multiple Views : this allows for different aggregations and uses of a single base
type.

A View is defined from the following:
* `name`: a string by which the View will be referred to, e.g. "rpc_latency". Names MUST be unique
within the library.
* `description`: a free format descriptive string, e.g. "RPC counts for last minute and hour"
* `Measure`: the Measure to which this view is applied. This field MAY be replaced with
`MeasureDescription` if defined.
* `columns`: an array of TagKeys. These values associated with tags of this name and type
form the basis by which individual stats will be aggregated (one aggregation per unique tag value).
If none are provided, then all data is recorded in a single aggregation.
* `aggregation`: `Distribution`, `Count`, `Sum`, `Mean`.

Implementations MUST define a View class, constructed from the parameters above. Views MAY have
getters for retrieving all of the information used in View definition. Once created, View metadata
is immutable.

Example in Java
```
// Define list of tags used to break down view - just rpc method in this case
ArrayList tagKeyListForMethodViews = new ArrayList();
tagKeyListForMethodViews.add(rpcMethodKey);

// Latencies from 100us to 10s.
BucketBoundaries latencyDistributionBuckets =
    new BucketBoundaries(Arrays.asList(0, 0.01, 0.1, 1.0, 10.0, 1000.0, 10000.0));

// We want a Distribution as well.
Distribution latencyDistribution = new Distribution.create(latencyDistributionBuckets);

// Define the view with full stats
View rpcLatencyByMethodView = new View(
  "rpc_latency_by_method", // name
  "Records full stats for RPC latency broken down by Method", // description
  rpc_latency, // Measure
  tagKeyListForMethodViews, // aggregation_tags
  latencyDistribution);
```

References to Views in the system MAY be obtained from querying of registered Views. This
functionality is required to decouple the definition of the data aggregation from the exporting
of the aggregated data.

Implementations MAY define a `ViewDescription` class which contains all the read-only fields from
the `View` definition such as: `name`, `description`, `MeasureDescription` and `columns`
and `Aggregation`.
