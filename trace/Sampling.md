# Sampling

This document is about the sampling bit, sampling decision, samplers and how and when does 
OpenCensus sample traces. A sampled traces is a traces that gets exported via the configured
exporters.

## Sampling Bit (propagated via TraceOptions)

Sampling bit is always set only at the start of a Span, and is using a `Sampler`

### What kind of samplers does OpenCensus support?
* `AlwaysSample` - sampler that always makes a "yes" decision every time.
* `NeverSample` - sampler that always makes a "no" decision every time.
* `Probability` - sampler that tries to uniformly sample traces with a given probability. When 
applied to a child `Span` of a **sampled** parent `Span` then it keeps the sampling decision.

### How can users control the Sampler that is used for sampling?
There are 2 ways to control the `Sampler` used when the library does sampling:
* Controlling the global default `Sampler` via [TraceConfig](https://github.com/census-instrumentation/opencensus-specs/blob/master/trace/TraceConfig.md).
* Pass a specific `Sampler` when start the [Span](https://github.com/census-instrumentation/opencensus-specs/blob/master/trace/Span.md)
(a.k.a. "span-scoped").
  * For example `AlwaysSample` and `NeverSample` can be used to implement request-specific 
  decisions such as those based on http paths.

### When does OpenCensus sample traces?
The OpenCensus library does sampling based on the following rules:
1. If the span is a root `Span` then a `Sampler` will be used to make the sampling decision:
   * If a "span-scoped" `Sampler` is provided, use it to determine the sampling decision.
   * Else use the global default `Sampler` to determine the sampling decision.
2. If the span is a child of a remote `Span` the sampling decision will be:
   * If a "span-scoped" `Sampler` is provided, use it to determine the sampling decision.
   * Else use the global default `Sampler` to determine the sampling decision.
2. If the span is a child of a local `Span` the sampling decision will be:
   * If a "span-scoped" `Sampler` is provided, use it to determine the sampling decision.
   * Else keep the sampling from the parent.
