# HTTP Attributes

This document summarizes HTTP attributes recorded from HTTP
requests and responses.

HTTP integrations should set the following attributes on the client
and server spans. For a server, request represents the incoming request.
For a client, request represents the outgoing request.

All attributes are optional.

| Attribute name            | Description                 | Example value                   |
|---------------------------|-----------------------------|---------------------------------|
| "http.host"               | Request URL host            | "example.com"                   |
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
| "http.host"               | "http.host"        |                    | "/http/host"              |
| "http.method"             | "http.method"      | "http.method"      | "/http/method"            |
| "http.path"               | "http.path"        |                    | "/http/path"              |
| "http.route"              | "http.route"       |                    | "/http/route"             |
| "http.user_agent"         |                    |                    | "/http/user_agent"        |
| "http.status_code"        | "http.status_code" | "http.status_code" | "/http/status_code"       |

Request body and response size of incoming and outgoing requests should be
represented as message events. Each redirect should be represented as a
message event.
