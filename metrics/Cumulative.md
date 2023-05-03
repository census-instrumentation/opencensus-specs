# Cumulative Overview
A `Cumulative` is used to record aggregated metrics that represents a single numerical value accumulated over a time interval. The value can only increase or be reset to zero on restart or reset the event. The recorded values are always >= 0. Typical examples of cumulative would be the number of elements added to a queue, total cpu usage, tasks completed, cache misses, etc.

If you need to track or record a value that goes up and down, use a [Gauge](./Gauge.md) API.

This document describes the key types and the overall behavior of API.

## Cumulative API

The value that is published for cumulative is an instantaneous measurement of an `int64` or `double` value. This API is useful when you want to manually increase and reset values as per service requirements.

The following general operations MUST be provided by the API:

* Defining a `name`, `description`, `unit`, `labelKeys`, `resource` and `constantLabels` which are fixed labels that always apply to a metric. This should give back the cumulative object to get or create time series, remove time series and clear all time series.
	* `name`: a string describing the name of the metric, e.g. "task_completed" or "total_errors". Names MUST be unique within the library. It is recommended to use names compatible with the intended end usage.
	* `description`: an optional string describing the metric, e.g."Total task completed". The default is set to "".
	* `unit`: an optional string describing the unit used for the metric. Follows the format described by
[Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html). The default set to "1".
	* `labelKeys`: an optional list of the label keys to track different types of metric. The default is set to empty list.
	* `constantLabels`: an optional map of label keys and label values. The default is set to empty map.
	* `resource`: an optional associated monitored resource information.
* Add a new time series with label values, which returns a `Point` (which is part of the `TimeSeries`). Each point represents an instantaneous measurement of a cumulative value. Each Cumulative Metric has one or more time series for a single metric.
	* `labelValues`: the list of label values. The number of label values must be the same to that of the label keys.
* The `Point` class should provide functionalities to manually increment/reset values. Example: `inc()` bye one, `inc(long times)` a specific number of times at once, `reset()`.
* Remove a single time series from the cumulative metric, if it is present.
	* `labelValues`: the list of label values.
* Clear all time series from the cumulative metric i.e. References to all previous point objects are invalid (not part of the metric).
* Get a default time series for a cumulative with all labels not set, or default labels.


It is recommended to keep a reference of a point for manual operations instead of always calling `getOrCreateTimeSeries` method. The keys in `labelKeys` must not be present in `constantLabels` map. Also, `constantLabels` will be added to all the timeseries for the Metric.

## Derived Cumulative API

The value that is published for cumulative is an instantaneous measurement of an `int64` or `double` value. This cumulative is self sufficient once created, so users should never need to interact with it. The value of the cumulative is observed from the `object` and a `callback function`. The callback function is invoked whenever metrics are collected, meaning the reported value is up-to-date.

The following general operations MUST be provided by the API:

* Defining a `name`, `description`, `unit`, `labelKeys`, `resource` and `constantLabels` which are fixed labels that always apply to a cumulative. This should give back cumulative object to add new time series, remove time series and clear all time series.
	* `name`: same as above name.
	* `description`: same as above description.
	* `unit`: same as above unit.
	* `labelKeys`: same as above labelKeys.
	* `constantLabels`: same as above constantLabels.
	* `resource`: same as above resource.
* Add a new time series with label values, an `object` and a `callback function`. The number of label values must be the same to that of the label keys.
	* `labelValues`: the list of label values. The number of label values must be the same to that of the label keys.
	* `object`: the state object from which the function derives a measurement.
	* `function`: the callback function to be called.
* Remove a single time series from the cumulative metric, if it is present.
	* `labelValues`: the list of label values.
* Clear all time series from the cumulative metric i.e. References to all previous point objects are invalid (not part of the metric).

In a time series, cumulative metric should have the same start time (set when creating a cumulative) and increasing end times, until an event resets then the start time should also be reset and we should use new start time for the following points.
