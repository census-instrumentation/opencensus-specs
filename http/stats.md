# HTTP Stats

Any particular library might provide only a subset of these measures/views/tags.
Check the language-specific documentation for the list of supported values.

All times (latencies) are measured in float64 fractions of microseconds.

There is no special support for multi-part HTTP requests and responses. These are just treated as one request.

## Units

Units are encoded according to the case-sensitive abbreviations from the [Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html):

* Latencies are measures in float64 milliseconds, denoted "ms"
* Sizes are measured in bytes, denoted "By"
* Dimensionless values have unit "1"

Buckets for distributions in default views are as follows:

* Size in bytes: 0, 1024, 2048, 4096, 16384, 65536, 262144, 1048576, 4194304, 16777216, 67108864, 268435456, 1073741824, 4294967296
* Latency in ms: 0, 1, 2, 3, 4, 5, 6, 8, 10, 13, 16, 20, 25, 30, 40, 50, 65, 80, 100, 130, 160, 200, 250, 300, 400, 500, 650, 800, 1000, 2000, 5000, 10000, 20000, 50000, 100000

## Client

All views, measures and tags should have the prefix: "opencensus.io/http/client". For example: "opencensus.io/http/client/started".

### Measures

Client stats are recorded for each individual HTTP request, including for each redirect (followed or not). 
Views are defined with the same name as the measure and the aggregation specified under Default View Aggregation.

| Measure suffix                           | Default View Aggregation | Description                                                                                                                                                                                                                                                                                       |
|------------------------------------------|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| opencensus.io/http/client/request_count  | sum                      | Number of all client requests started                                                                                                                                                                                                                                                             |
| opencensus.io/http/client/request_bytes  | distribution             | Total bytes sent in request body (not including headers). This is uncompressed bytes.                                                                                                                                                                                                             |
| opencensus.io/http/client/response_bytes | distribution             | Total bytes received in response bodies (not including headers but including error responses with bodies). Should be measured from actual bytes received and read, not the value of the Content-Length header. This is uncompressed bytes. Responses with no body should record 0 for this value. |
| opencensus.io/http/client/latency        | distribution             | Time between first byte of request headers sent to last byte of response received, or terminal error                                                                                                                                                                                              |

### Tags

All client metrics should be tagged with the following.

| Tag suffix  | Description                                                          |
|-------------|----------------------------------------------------------------------|
| http.method | HTTP method, capitalized (i.e. GET, POST, PUT, DELETE, etc.)         |
| http.status | HTTP status code, or "error" if no response status line was received |
| http.path   | URL path (not including query string)                                |
| http.host   | Value of the request Host header                                     |

### Default views

The following default views are also defined:

| View suffix                                             | Measure suffix | Aggregation | Tags        |
|---------------------------------------------------------|----------------|-------------|-------------|
| opencensus.io/http/client/response_count_by_status_code | latency        | count       | status_code |
| opencensus.io/http/client/request_count_by_method       | latency        | count       | method      |

## Server

TBD but most will mirror client metrics.
