# HTTP Stats

All views and measures have the prefix: "opencensus.io/http". Any particular library might provide only a subset of these measures/views/tags.

All times (latencies) are measured in float64 fractions of microseconds.

There is no special support for multi-part HTTP requests and responses. These are just treated as one request.

## Client

All views, measures and tags should have the prefix: "opencensus.io/http/client". For example: "opencensus.io/http/client/started".

### Measures

Client stats are recorded for each individual HTTP request, including for each redirect (followed or not). 

| Measure suffix     | Default View Aggregation | Description                                                                                                                                                                                                                                                                                       |
|--------------------|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| started            | sum                      | Number of all client requests started                                                                                                                                                                                                                                                             |
| bytes_sent         | sum                      | Total bytes sent in request body (not including headers). This is uncompressed bytes.                                                                                                                                                                                                             |
| bytes_recv         | sum                      | Total bytes received in response bodies (not including headers but including error responses with bodies). Should be measured from actual bytes received and read, not the value of the Content-Length header. This is uncompressed bytes. Responses with no body should record 0 for this value. |
| headers_sent       | none                     | Total number of header lines sent in outgoing requests, not including trailing headers                                                                                                                                                                                                            |
| headers_recv       | none                     | Total number of header lines received in responses, not including trailing headers                                                                                                                                                                                                                |
| latency            | distribution             | Time between first byte of request headers sent to last byte of response received, or terminal error                                                                                                                                                                                              |
| connections_opened | count                    | Number of underlying transport (TCP/TLS) connections opened                                                                                                                                                                                                                                       |
| connections_closed | count                    | Number of underlying transport (TCP/TLS) connections closed                                                                                                                                                                                                                                       |

### Tags

All client metrics should be tagged with the following.

| Tag suffix    | Description                                                    |
|---------------|----------------------------------------------------------------|
| method        | HTTP method, capitalized (i.e. GET, POST, PUT, DELETE, etc.)   |
| status_code   | HTTP status code, or 0 if no response status line was received |
| response_type | Response media type, if applicable                             |
| request_type  | Request media type, if applicable                              |
| version       | HTTP version in request                                        |
| path          | URL path (not including query string)                          |
| host          | Value of the request Host header                               |

### Default views

The following default views are also defined:

| View suffix          | Measure suffix | Aggregation  | Tags        |
|----------------------|----------------|--------------|-------------|
| count_by_status_code | latency        | count        | status-code |
| ended                | latency        | count        | none        |
| latency_by_path      | latency        | distribution | path        |

## Server

TBD but most will mirror client metrics.
