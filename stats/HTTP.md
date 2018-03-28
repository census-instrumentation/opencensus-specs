# HTTP Stats

Any particular library might provide only a subset of these measures/views/tags.
Check the language-specific documentation for the list of supported values.

There is no special support for multi-part HTTP requests and responses. These are just treated as a single request.

## Units

As always, units are encoded according to the case-sensitive abbreviations from the [Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html):

* Latencies are measures in float64 milliseconds, denoted "ms"
* Sizes are measured in bytes, denoted "By"
* Dimensionless values have unit "1"

Buckets for distributions in default views are as follows:

* Size in bytes: 0, 1024, 2048, 4096, 16384, 65536, 262144, 1048576, 4194304, 16777216, 67108864, 268435456, 1073741824, 4294967296
* Latency in ms: 0, 1, 2, 3, 4, 5, 6, 8, 10, 13, 16, 20, 25, 30, 40, 50, 65, 80, 100, 130, 160, 200, 250, 300, 400, 500, 650, 800, 1000, 2000, 5000, 10000, 20000, 50000, 100000

## Client

### Measures

Client stats are recorded for each individual HTTP request, including for each individual redirect (followed or not).

| Measure name                                | Unit | Description                                                                                                                                                                                                                                                                                       |
|---------------------------------------------|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| opencensus.io/http/client/sent_bytes        | By   | Total bytes sent in request body (not including headers). This is uncompressed bytes.                                                                                                                                                                                                             |
| opencensus.io/http/client/received_bytes    | By   | Total bytes received in response bodies (not including headers but including error responses with bodies). Should be measured from actual bytes received and read, not the value of the Content-Length header. This is uncompressed bytes. Responses with no body should record 0 for this value. |
| opencensus.io/http/client/roundtrip_latency | ms   | Time between first byte of request headers sent to last byte of response received, or terminal error                                                                                                                                                                                              |

### Tags

All client metrics should be tagged with the following.

| Tag suffix         | Description                                                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------|
| http.client_method | HTTP method, capitalized (i.e. GET, POST, PUT, DELETE, etc.)                                             |
| http.client_path   | URL path (not including query string)                                                                    |
| http.client_status | HTTP status code as an integer (e.g. 200, 404, 500.), or "error" if no response status line was received |
| http.client_host   | Value of the request Host header                                                                         |

### Default views

The following set of views are considered minimum required to monitor client side performance:

| View name                                   | Measure suffix    | Aggregation  | Tags suffix                               |
|---------------------------------------------|-------------------|--------------|-------------------------------------------|
| opencensus.io/http/client/sent_bytes        | sent_bytes        | distribution | client_method, client_path                |
| opencensus.io/http/client/received_bytes    | received_bytes    | distribution | client_method, client_path                |
| opencensus.io/http/client/roundtrip_latency | roundtrip_latency | distribution | client_method, client_path                |
| opencensus.io/http/client/completed_count   | roundtrip_latency | count        | client_method, client_path, client_status |

## Server

Server measures are recorded at the end of request processing.

### Measures

Server stats are recorded for each individual HTTP request, including for each redirect (followed or not). 

| Measure name                             | Unit | Description                                                                                                                                                                                                                                                                                   |
|------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| opencensus.io/http/server/received_bytes | By   | Total bytes received in request body (not including headers). This is uncompressed bytes.                                                                                                                                                                                                     |
| opencensus.io/http/server/sent_bytes     | By   | Total bytes sent in response bodies (not including headers but including error responses with bodies). Should be measured from actual bytes received and read, not the value of the Content-Length header. This is uncompressed bytes. Responses with no body should record 0 for this value. |
| opencensus.io/http/server/server_latency | ms   | Time between first byte of request headers read to last byte of response sent, or terminal error                                                                                                                                                                                              |

### Tags

All server metrics should be tagged with the following. 
`http.server_method`, `http.server_path`, `http.server_host` are available in the context for the entire request processing.
`http.server_status` is only available around the stats recorded at the end of request processing.

| Tag suffix         | Description                                                         |
|--------------------|---------------------------------------------------------------------|
| http.server_method | HTTP method, capitalized (i.e. GET, POST, PUT, DELETE, etc.)        |
| http.server_path   | URL path (not including query string)                               |
| http.server_status | HTTP server status code returned, as an integer e.g. 200, 404, 500. |
| http.server_host   | Value of the request Host header                                    |

### Default views

The following set of views are considered minimum required to monitor server side performance:

| View name                                 | Measure suffix | Aggregation  | Tags suffix                               |
|-------------------------------------------|----------------|--------------|-------------------------------------------|
| opencensus.io/http/server/received_bytes  | received_bytes | distribution | server_method, server_path                |
| opencensus.io/http/server/sent_bytes      | sent_bytes     | distribution | server_method, server_path                |
| opencensus.io/http/server/server_latency  | server_latency | distribution | server_method, server_path                |
| opencensus.io/http/server/completed_count | server_latency | count        | server_method, server_path, server_status |
