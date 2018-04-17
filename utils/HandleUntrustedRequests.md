# Handle Untrusted Requests

This document is about how to handle untrusted requests for trace and tags.

## What is an untrusted request?
An untrusted request refers to a request coming from an external untrusted source (e.g. external
customer request).

## How does OpenCensus know if a request is trusted or not?
The OpenCensus library does not have a mechanism to determine if an incoming request is trusted 
or not, because of this the application owner should configure the library accordingly.

The OpenCensus library should allow to configure this information in the server plugins (grpc, 
http) for each server instance.

## Why not trusting all the requests?
For trace we do not trust the incoming requests (trace headers) because:
* Users can send always sampled requests all the time (by mistake, or malicious user).
* Users can send always the same trace id (e.g. malicious user).

For tags we do not trust the incoming requests (tags headers) because:
* Users can send PII data (by mistake).
* Users can send garbage data (e.g. malicious user).

These are only few examples why trusting the incoming trace and tags headers may cause problems
when the request comes from an untrusted client.

## What to do with trace headers from an untrusted request?
When received an untrusted request the library should do the following:
* Start a new trace (generate a new trace id) and apply the default sampling rate or allow users
to configure the sampling rate (downside of allowing users to set a specific sampling rate is the
fact that they cannot change this easily at runtime via [TraceConfig](../trace/TraceConfig.md)).
* Use the incoming trace header (trace_id, span_id) to record a [Link][SpanDataModel] to the newly
created `Span`.

Because of the lower probability to have both incoming request and the the newly generated trace
sampled at the same time, the library should have a way to pass the trusting information to the
`Sampler` interface to allow users to implement smart Samplers (e.g. high sampling probability if
the incoming untrusted request is sampled).

// DO_NOT_SUBMIT: take feedback what is better 2 default samplers in TraceConfig (one for trusted
ond for untrusted) or pass the untrusted bit to the sampler (this may have the problem that
ProbabilitySamplers will have multiple sampled probabilities 1 for trusted requests 1 for
untrusted sampled requests 1 for untrusted not-sampled requests).

## What to do with tags headers from an untrusted request?
The simplest solution to implement is to drop all the incoming tags, but there may be cases where
users may want to propagate only some of the tags. The initial version of this should simply drop
all the tags, but later when better filters are defined the library should allow users to
configure what tags to accept.

[SpanDataModel]: https://github.com/census-instrumentation/opencensus-proto/blob/master/opencensus/proto/trace/trace.proto