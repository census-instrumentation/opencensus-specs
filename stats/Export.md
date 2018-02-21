# Export API Overview
The stats library aggregates measurements into Views (which are essentially a collection of
metrics, each with a different set of labels). All output objects are immutable. The core data 
types used are:
* `AggregationData`: contains data from an `Aggregation`.
* `ViewData`: encapsulates `View` data aggregation.

### AggregationData
An AggregationData describes the result of data aggregation. The library SHOULD define an
AggregationData for each Aggregation types defined.

The library SHOULD provide support for multiple types of Aggregations:
* `CountData`: data generated for a `Count` aggregation.
* `SumDataDouble` and `SumDataInt64`: data generated for a `Sum` aggregation based on the `Measure`
type.
* `MeanData`: data generated for a `Mean` aggregation.
* `DistributionData`: data generated for a `Distribution` aggregation.

### ViewData
A ViewData is defined from the following:
* `name`: the name of the exported `View`.
* `columns`: the `columns` of the exported `View`.
* `Measure`: the `Measure` of the exported `View`. This field MAY be replaced with
`MeasureDescription` if defined.
* `aggregation`: the `aggregation` of the exported `View`
* `rows`: a map of repeated tag values, and, dependent on Aggregation type in the `View` an
`AggregationData` object. These contain the respective tag values corresponding to the `columns`
parameter from the `View` and the aggregated data associated with those `columns`.
* `start_time`: a timestamp, describing the start time at which the underlying View started
collection of these data.
* `end_time`: a timestamp, describing the end time at which the underlying View ended collection
of these data.

The library SHOULD provide a means of retrieving the ViewData for any registered view in the system.
