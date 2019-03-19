# Gauge Overview
A `Gauge` is used to record aggregated metrics that can go up and down. Typical examples of gauges would be the number of jobs/entries in a queue,  number of threads in a running state or current memory usage etc.

The `Gauge` values can be negative. This document describes the key types and the overall behavior of API.

## Gauge API

The value that is published for gauges is an instantaneous measurement of an `int64` or `double` value. This API is useful when you want to manually increase and decrease values as per service requirements.

The following general operations MUST be provided by the API:
* Defining a `name`, `description`, `unit`, `labelKeys`, `resource` and `constantLabels` which are fixed labels that always apply to a gauge. This should give back the gauge object to get or create time series, remove time series and clear all time series.
	* `name`: a string describing the name of the metric, e.g. "vm_cpu_cycles" or "queue_size". Names MUST be unique within the library. It is recommended to use names compatible with the intended end usage.
	* `description`: a string describing the metric, e.g."Virtual cycles executed on VM".
	* `unit`: a string describing the unit used for the metric (default set to "1"). Follows the format described by
[Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html).
	* `labelKeys`: the list of the label keys to track different types of metric.
	* `constantLabels`: the map of label keys and label values. The keys in `labelKeys` must not be present in this map.
	* `resource`: the optional associated monitored resource information.
* Add a new time series with label values, which returns a `Point` (which is part of the `TimeSeries`). Each point represents an instantaneous measurement of a varying gauge value. Each Gauge Metric has one or more time series for a single metric.
	* `labelValues`: the list of label values. The number of label values must be the same to that of the label keys.
* The `Point` class should provide functionalities to manually increment/decrement values. Example: `add(long amt)`, `set(long value)`.
* Remove a single time series from the gauge metric, if it is present.
	* `labelValues`: the list of label values.
* Clear all time series from the gauge metric i.e. References to all previous point objects are invalid (not part of the metric).
* Get a default time series for a gauge with all labels not set, or default labels.

Example in Java:
```java
private static final MetricRegistry metricRegistry = Metrics.getMetricRegistry();

List<LabelKey> labelKeys = Arrays.asList(LabelKey.create("key1", "description"));
List<LabelValue> labelValues = Arrays.asList(LabelValue.create("queue1"));
LabelKey constantLabelKey = LabelKey.create("hostname", "hostname");
LabelValue constantLabelValue = LabelValue.create("localhost");

Map<LabelKey, LabelValue> constantLabels = Collections.singletonMap(constantLabelKey, constantLabelValue);

LongGauge gauge = metricRegistry.longGaugeBuilder()
    .setName("queue_size")
    .setDescription("The number of jobs")
    .setLabelKeys(labelKeys)
    .setConstantLabels(constantLabels)
    .build();

LongPoint point = gauge.getOrCreateTimeSeries(labelValues);
 
void doSomeWork() {
   point.set(15);
}
```
It is recommended to keep a reference of a point for manual operations instead of always calling `getOrCreateTimeSeries` method.

## Derived Gauge API

The value that is published for gauges is an instantaneous measurement of an `int64` or `double` value. This gauge is self sufficient once created, so users should never need to interact with it. The value of the gauge is observed from the `object` and a `callback function`. The callback function is invoked whenever metrics are collected, meaning the reported value is up-to-date.

The following general operations MUST be provided by the API:
* Defining a `name`, `description`, `unit`, `labelKeys`, `resource` and `constantLabels` which are fixed labels that always apply to a gauge. This should give back gauge object to add new time series, remove time series and clear all time series.
	* `name`: a string describing the name of the metric, e.g. "vm_cpu_cycles". Names MUST be unique within the library. It is recommended to use names compatible with the intended end usage.
	* `description`: a string describing the metric, e.g."Virtual cycles executed on VM".
	* `unit`: a string describing the unit used for the metric (default set to "1"). Follows the format described by
[Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html).
	* `labelKeys`: the list of the label keys to track different types of metric.
	* `constantLabels`: the map of label keys and label values. The keys in `labelKeys` must not be present in this map.
	* `resource`: the optional associated monitored resource information.
* Add a new time series with label values, an `object` and a `callback function`. The number of label values must be the same to that of the label keys.
	* `labelValues`: the list of label values. The number of label values must be the same to that of the label keys.
	* `object`: the state object from which the function derives a measurement.
	* `function`: the callback function to be called.
* Remove a single time series from the gauge metric, if it is present.
	* `labelValues`: the list of label values.
* Clear all time series from the gauge metric i.e. References to all previous point objects are invalid (not part of the metric).

Example in Java:
```java
private static final MetricRegistry metricRegistry = Metrics.getMetricRegistry();

List<LabelKey> labelKeys = Arrays.asList(LabelKey.create("key1", "description"));
List<LabelValue> labelValues = Arrays.asList(LabelValue.create("value1"));
LabelKey constantLabelKey = LabelKey.create("hostname", "hostname");
LabelValue constantLabelValue = LabelValue.create("localhost");

Map<LabelKey, LabelValue> constantLabels = Collections.singletonMap(constantLabelKey, constantLabelValue);

DerivedDoubleGauge gauge = metricRegistry.derivedDoubleGaugeBuilder()
    .setName("vm_cpu_cycles")
    .setDescription("Virtual cycles executed on VM")
    .setLabelKeys(labelKeys)
    .setConstantLabels(constantLabels)
    .build();

gauge.createTimeSeries(labelValues, cpuInfo,
      new ToDoubleFunction<CpuInfo>() {
        {@literal @}Override
        public double applyAsDouble(CpuInfo cpuInfo) {
          return cpuInfo.cycles();
        }
   });
```
