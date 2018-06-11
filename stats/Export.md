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
* `LastValueDataDouble` and `LastValueDataInt64`: data generated for a `LastValue` aggregation based 
on the `Measure` type.
* `DistributionData`: data generated for a `Distribution` aggregation. If there is a histogram in 
the `Distribution`, `DistributionData` MAY contain a list of `Exemplar`s.

### ViewData
A ViewData is defined from the following:
* `View`: the exported `View`.
* `rows`: a map of repeated tag values, and, dependent on Aggregation type in the `View` an
`AggregationData` object. These contain the respective tag values corresponding to the `columns`
parameter from the `View` and the aggregated data associated with those `columns`.
* `start_time`: a timestamp, describing the start time of the current stats snapshot.
* `end_time`: a timestamp, describing the end time of the current stats snapshot.

The library SHOULD provide a means of retrieving the ViewData for any registered view in the system.

ViewData is a language-specific data structure implementing the [Metrics data
model](Metrics.md).

### Aggregation to Metric

| Aggregation  | Measure Type    | Metric Type  | Value Type   | Unit               |
|--------------|-----------------|--------------|--------------|--------------------|
| Count        | Int64 or Double | CUMULATIVE   | INT64        | Dimensionless Unit |
| Sum          | Double          | CUMULATIVE   | DOUBLE       | Measure Unit       |
| Sum          | Int64           | CUMULATIVE   | INT64        | Measure Unit       |
| LastValue    | Double          | GAUGE        | DOUBLE       | Measure Unit       |
| LastValue    | Int64           | GAUGE        | INT64        | Measure Unit       |
| Distribution | Int64 or Double | CUMULATIVE   | DISTRIBUTION | Measure Unit       |

Dimensionless Unit can be represented as "1".
