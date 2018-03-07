# HTTP

This document explains tracing of HTTP requests with OpenCensus.

## Spans

Implementations MUST create a span for outgoing requests at the
client and a span for incoming requests at the server.

Span name is formatted as:

* /$path for outgoing requests.
* /($path|$route) for incoming requests.

If route cannot be determined, path is used to name the
the span for outgoing requests.

Port MUST be omitted if it is 80 or 443.

Examples of span names:

* /users
* /messages/[:id]
* /users/25f4c31d

Outgoing requests should be a span kind of CLIENT and
incoming requests should be a span kind of SERVER.

## Propagation

Propagation is how SpanContext is transmitted on the wire
in an HTTP request.

Implementations MUST allow users to set their own propagation
format and MUST provide an implementation for B3 at least.

If user doesn't set any propagation methods explicitly, B3 is used.

The propagation method SHOULD modify a request object to insert
a SpanContext or SHOULD be able to extract a SpanContext from a
request object.

## Status

Implementations MUST set status if HTTP request or response
is not successful (e.g. not 2xx). In redirection case, if
the client doesn't have autoredirection support, request
should be considered successful.

Set status code to UNKNOWN (2) if the reason cannot be inferred
at the callsite or from the HTTP status code.

Don't set the status message if the reason can be inferred at
the callsite of from the HTTP status code.

## Message events

In the lifetime of an incoming and outgoing request, the following
message events SHOULD be created:

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

Implementations SHOULD set the following attributes on the client
and server spans. For a server, request represents the incoming request.
For a client, request represents the outgoing request.

All attributes are optional.

| Attribute name            | Description                 | Example value                   |
|---------------------------|-----------------------------|---------------------------------|
| "http.host"               | Request URL host            | "example.com"                   |
| "http.port"               | Request URL port            | 443                             |
| "http.method"             | Request URL method          | "GET"                           |
| "http.path"               | Request URL path            | "/users/25f4c31d"               |
| "http.route"              | Matched request URL route   | "/users/:userID"                |
| "http.user_agent"         | Request user-agent          | "HTTPClient/1.2"                |
| "http.status_code"        | Response status code        | 200                             |

Exporters should always export the collected attributes.
Exporters should map the collected attributes to backend's
known attributes/labels.

The following table summarizes how OpenCensus attributes maps to the
known attributes/labels on supported tracing backends.

| OpenCensus attribute      | Zipkin             | Jaeger             | Stackdriver Trace label   |
|---------------------------|--------------------|--------------------|---------------------------|
| "http.host"               | "http.host"        | "http.host"        | "/http/host"              |
| "http.method"             | "http.method"      | "http.method"      | "/http/method"            |
| "http.path"               | "http.path"        | "http.path"        | "/http/path"              |
| "http.port"               | "http.port"        | "http.port"        | "http.port"               |
| "http.route"              | "http.route"       | "http.route"       | "/http/route"             |
| "http.user_agent"         | "http.user_agent"  | "http.user_agent"  | "/http/user_agent"        |
| "http.status_code"        | "http.status_code" | "http.status_code" | "/http/status_code"       |
