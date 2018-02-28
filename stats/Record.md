# Record API Overview
The stats library allows users to record metrics for their applications or libraries. The core 
data types used are:
* `Measure`: describes the type of the individual values recorded by an application.
* `Measurement`: describes a data point to be collected for a `Measure`.

## Measure
A `Measure` describes the type of the individual values/measurements recorded by an application. It
includes information such as the type of measurement, the units of measurement and descriptive names
for the data. This provides the fundamental type used for recording data.

A Measure describes a value with the following metadata:
* `name`: a string by which the measure will be referred to, e.g. "rpc_server_latency", or
"vm_cpu_cycles". Names MUST be unique within the library. It is recommended to use names 
compatible with the intended end usage, e.g, use host/path pattern.
* `description`: a string describes the measure, e.g. "RPC latency in seconds", "Virtual cycles
executed on VM".
* `unit`: a string describing the unit used for the `Measure`. Follows the format described by
[Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html).
* `type`: the only supported types are `int64` and `double`.

Implementations MAY define a `Measure` data type, constructed from the parameters above. 
Measure MAY have getters for retrieving all of the information used in `Measure` definition. 
Once created, Measure metadata is immutable.

Example in Java
```java
private static final MeasureDouble RPC_LATENCY =
    MeasureDouble.create("grpc.io/latency", "latency", "ms");
```

References to Measures in the system MAY be obtained from querying of registered Measures. This
functionality is required to decouple the recording of the data from the exporting of the data.

For languages that do not allow private properties/metadata and if they are needed implementations 
MAY define a `MeasureDescription` data type which contains all the read-only fields  from the 
`Measure` definition such as: `name`, `description`, `unit` and `type`.

## Measurement
A `Measurement` is defined from the following:
* `Measure`: the `Measure` to which this `value` is applied.
* `value`: recorded value, MUST have the appropriate type to match the `Measure` definition.

Implementations MAY define a `MeasurementMap` which describes a set of data points to be collected
for a set of Measures. Adding this functionality may improve the efficiency of the record usage API.

## Recording Stats

Users should record Measurements against a context, either an explicit context or the implicit 
current context. Tags from the context are recorded with the Measurements if they are any.

Implementations SHOULD provide a means of recording multiple Measurements at once. This 
functionality can be provided through one of the following options:
* As a method in a class/package (recommended name Stats/stats), taking a list of Measurement as
argument. e.g. `record(List<Measurement>)` or `record(...Measurement)`.
* As a `record` method of the appropriate data type. e.g. `MeasurementMap.record()`.

Example in Java

```java
private static final MeasureDouble RPC_LATENCY =
    MeasureDouble.create("grpc.io/client/latency", "latency", "ms");
private static final MeasureLong RPC_BYTES_SENT =
    MeasureLong.create("grpc.io/client/bytes_sent", "bytes sent", "kb");

MeasurementMap measurementMap = new MeasurementMap();
measurementMap.put(RPC_LATENCY, 10.3);
measurementMap.put(RPC_BYTES_SENT, 124);
measurementMap.record();  // reads context from thread-local.
}
```