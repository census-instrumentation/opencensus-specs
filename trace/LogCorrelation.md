# Log Correlation

Log correlation is a feature that inserts information about the current span into log entries
created by existing logging frameworks.  The feature can be used to add more context to log entries,
filter log entries by trace ID, or show log entries as annotations on a trace.

The design of a log correlation implementation depends heavily on the details of the particular
logging framework that it supports.  Therefore, this document only covers the aspects of log
correlation that could be shared across log correlation implementations for multiple languages and
logging frameworks.  It doesn't cover how to hook into the logging framework.

## Tracing data to include in log entries

A log correlation implementation should insert the following pieces of tracing data from the current
span context into each log entry:

### Trace ID

The trace ID of the current span.  See [Span.md#traceid](Span.md#traceid).

### Span ID

The span ID of the current span.  See [Span.md#spanid](Span.md#spanid).

### Sampling Decision

The sampling bit of the current span, as a boolean.  See
[Span.md#supported-bits](Span.md#supported-bits).

## String format for tracing data

The logging framework may require the pieces of tracing data to be converted to strings.  In that
case, the log correlation implementation should format the trace ID and span ID as lowercase base 16
and format the sampling decision as "true" or "false".

## Key names for tracing data

Some logging frameworks allow the insertion of arbitrary key-value pairs into log entries.  When
a log correlation implementation inserts tracing data by that method, the key names should be
"opencensusTraceId", "opencensusSpanId", and "opencensusTraceSampled" by default.  The log
correlation implementation may allow the user to override the tracing data key names.

## Deciding when to add tracing data to a log entry

The log correlation implementation may allow the user to configure the choice to add tracing data to
a log entry.  This configuration option should be called "span selection", though the format and
case of the option name may depend on the method of configuration.  For example, a Java logging
property name might include a class name, as in
"io.opencensus.logcorrelation.OpenCensusTracingDataInserter.spanSelection".  Span selection should
have the following values, also with a format and case that depends on the method of configuration:

### All Spans

Always add tracing data to log entries, even when the current span is not sampled.  This is the
default.

### Sampled Spans

Add tracing data to a log entry iff the current span is sampled.

### No Spans

Never add tracing data to log entries.  This option disables the log correlation feature.
