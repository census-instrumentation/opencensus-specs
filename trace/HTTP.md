# HTTP Trace

This document explains tracing of HTTP requests with OpenCensus.

## Spans

Implementations MUST create a span for outgoing requests at the client and a span for incoming 
requests at the server.

Span name is formatted as:

* /$path for outgoing requests.
* /($path|$route) for incoming requests.

If route cannot be determined, path is used to name the the span for outgoing requests.

Port MUST be omitted if it is 80 or 443.

Examples of span names:

* /users
* /messages/[:id]
* /users/25f4c31d

Outgoing requests should be a span kind of CLIENT and
incoming requests should be a span kind of SERVER.

## Propagation

Propagation is how SpanContext is transmitted on the wire in an HTTP request.

Implementations MUST allow users to set their own propagation format and MUST provide an 
implementation for B3 at least.

If user doesn't set any propagation methods explicitly, B3 is used.

The propagation method SHOULD modify a request object to insert a SpanContext or SHOULD be able 
to extract a SpanContext from a request object.

## Status

Implementations MUST set status if HTTP request or response is not successful (e.g. not 2xx). In 
redirection case, if the client doesn't have autoredirection support, request should be 
considered successful.

Set status code to UNKNOWN (2) if the reason cannot be inferred at the callsite or from the HTTP 
status code.

Don't set the status message if the reason can be inferred at the callsite of from the HTTP 
status code.

### Mapping from HTTP status codes to Trace status codes

| HTTP code             | Trace status code      |
|-----------------------|------------------------|
| 0...199               | 2 Unknown              |
| 200...399             | 0 (OK)                 |
| 400 Bad Request       | 3 (INVALID_ARGUMENT)   |
| 504 Gateway Timeout   | 4 (DEADLINE_EXCEEDED)  |
| 404 Not Found         | 5 (NOT_FOUND)          |
| 403 Forbidden         | 7 (PERMISSION_DENIED)  |
| 401 Unauthorized*     | 16 (UNAUTHENTICATED)   |
| 429 Too Many Requests | 8 (RESOURCE_EXHAUSTED) |
| 501 Not Implemented   | 12 (UNIMPLEMENTED)     |
| 503 Unavailable       | 14 (UNAVAILABLE)       |

Notes: 401 Unauthorized actually means unauthenticated according to RFC 7235, 3.1.

The Status message should be the Reason-Phrase (RFC 2616 6.1.1) from the response status line (if available).

## Message events

In the lifetime of an incoming and outgoing request, the following message events SHOULD be created:

* A message event for the request body size if/when determined.
* A message event for the response size if/when determined.

Implementations SHOULD create message event when body size is determined.

```
-> [time], MessageEventTypeSent, UncompressedByteSize, CompressedByteSize
```

Implementations SHOULD create message event when response size is determined.

```
-> [time], MessageEventTypeRecv, UncompressedByteSize, CompressedByteSize
```

## Attributes

Implementations SHOULD set the following attributes on the client and server spans. For a server,
 request represents the incoming request. For a client, request represents the outgoing request.

All attributes are optional.

| Attribute name            | Description                 | Example value                   |
|---------------------------|-----------------------------|---------------------------------|
| "http.host"               | Request URL host            | "example.com:779"               |
| "http.method"             | Request URL method          | "GET"                           |
| "http.path"               | Request URL path            | "/users/25f4c31d"               |
| "http.route"              | Matched request URL route   | "/users/:userID"                |
| "http.user_agent"         | Request user-agent          | "HTTPClient/1.2"                |
| "http.status_code"        | Response status code        | 200                             |

Exporters should always export the collected attributes. Exporters should map the collected 
attributes to backend's known attributes/labels.

The following table summarizes how OpenCensus attributes maps to the
known attributes/labels on supported tracing backends.

| OpenCensus attribute      | Zipkin             | Jaeger             | Stackdriver Trace label   |
|---------------------------|--------------------|--------------------|---------------------------|
| "http.host"               | "http.host"        | "http.host"        | "/http/host"              |
| "http.method"             | "http.method"      | "http.method"      | "/http/method"            |
| "http.path"               | "http.path"        | "http.path"        | "/http/path"              |
| "http.route"              | "http.route"       | "http.route"       | "/http/route"             |
| "http.user_agent"         | "http.user_agent"  | "http.user_agent"  | "/http/user_agent"        |
| "http.status_code"        | "http.status_code" | "http.status_code" | "/http/status_code"       |

## Sampling

There are two ways to control the `Sampler` used:
* Controlling the global default `Sampler` via [TraceConfig](https://github.com/census-instrumentation/opencensus-specs/blob/master/trace/TraceConfig.md).
* Pass a specific `Sampler` as an option to the HTTP plugin. Plugins should support setting
a sampler per HTTP request.

Example cases where per-request sampling is useful:

- Having different sampling policy per route
- Having different sampling policy per method
- Filtering out certain paths (e.g. health endpoints) to disable tracing
- Always sampling critical paths
- Sampling based on the custom request header or query parameter

In the following Go example, incoming and outgoing request objects can
dynamically inspected to set a sampler.

For outgoing requests:

```go
type Transport struct {
 	// GetStartOptions allows to set start options per request.
	GetStartOptions func(*http.Request) trace.StartOptions

	// ...
}
```

For incoming requests:

```go
type Handler struct {
 	// GetStartOptions allows to set start options per request.
	GetStartOptions func(*http.Request) trace.StartOptions

	// ...
}
```
