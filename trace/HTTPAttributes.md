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
| "http.path"               | Request URL path            | "/users"                        |
| "http.user_agent"         | Request user-agent          | "HTTPClient/1.2"                |
| "http.status_code"        | Response status code        | 200                             |
| "http.request_body_size"  | Request body size in bytes  | 64328                           |
| "http.response_body_size" | Response body size in bytes | 128                             |

The following table summarizes how OpenCensus attributes maps to the
known attributes/labels on tracing backends.

| OpenCensus attribute      | Stackdriver Trace label                     |
|---------------------------|---------------------------------------------|
| "http.host"               | "trace.cloud.google.com/http/host"          |
| "http.method"             | "trace.cloud.google.com/http/method"        |
| "http.path"               |                                             |
| "http.user_agent"         | "trace.cloud.google.com/http/user_agent"    |
| "http.status_code"        | "trace.cloud.google.com/http/status_code"   |
| "http.request_body_size"  | "trace.cloud.google.com/http/request/size"  |
| "http.response_body_size" | "trace.cloud.google.com/http/response/size" |
