# gRPC Stats

Any particular library might provide only a subset of these measures/views/tags.
Check the language-specific documentation for the list of supported values.

## Units

Units are encoded according to the case-sensitive abbreviations from the [Unified Code for Units of Measure](http://unitsofmeasure.org/ucum.html):

* Latencies are measures in float64 milliseconds, denoted "ms"
* Sizes are measured in bytes, denoted "By"
* Dimensionless values have unit "1"

Buckets for distributions in default views are as follows:

* Size in bytes: 0, 1024, 2048, 4096, 16384, 65536, 262144, 1048576, 4194304, 16777216, 67108864, 268435456, 1073741824, 4294967296
* Latency in ms: 0, 0.01, 0.05, 0.1, 0.3, 0.6, 0.8, 1, 2, 3, 4, 5, 6, 8, 10, 13, 16, 20, 25, 30, 40, 50, 65, 80, 100, 130, 160, 200, 250, 300, 400, 500, 650, 800, 1000, 2000, 5000, 10000, 20000, 50000, 100000
* Counts (no unit): 0, 1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048, 4096, 8192, 16384, 32768, 65536

## Client

### Measures

Client stats are recorded for each individual gRPC request. All measurements except `grpc.io/client/started_count` must be recorded at the end of each gRPC request.

| Measure name                               | Unit | Description                                                                                   |
|--------------------------------------------|------|-----------------------------------------------------------------------------------------------|
| grpc.io/client/started_count               | 1    | Number of all client requests started. Records "1" at the beginning of each gRPC request.     |
| grpc.io/client/request_messages_count      | 1    | Number of request messages sent in a gRPC request. Has value "1" for non-streaming RPCs.      |
| grpc.io/client/request_bytes               | By   | Total compressed bytes sent in total across all request messages in a gRPC request.           |
| grpc.io/client/uncompressed_request_bytes  | By   | Total uncompressed bytes sent in total across all request messages in a gRPC request.         |
| grpc.io/client/response_messages_count     | 1    | Number of response messages received in a gRPC request. Has value "1" for non-streaming RPCs. |
| grpc.io/client/response_bytes              | By   | Total compressed bytes received in total across all response messages in a gRPC request.      |
| grpc.io/client/uncompressed_response_bytes | By   | Total uncompressed bytes received in total across all response messages in a gRPC request.    |
| grpc.io/client/latency                     | ms   | Time between first byte of request sent to last byte of response received, or terminal error. |
| grpc.io/client/server_latency              | ms   | Propagated from the server and should have the same value as "grpc.io/server/latency".        |

### Tags

All client metrics should be tagged with the following. Some tags are not available for measurements recorded at the beginning of the gRPC request (e.g. `grpc.canonical_status`).

| Tag name           | Description                                                   |
|--------------------|---------------------------------------------------------------|
| grpc.client_method | Full gRPC method name, including package, service and method. |
| grpc.client_status | gRPC client status code.                                      |

### Default views

The following set of views are considered minimum required to monitor client side performance:

| View name                                   | Measure suffix              | Aggregation  | Tags suffix                  |
|---------------------------------------------|-----------------------------|--------------|------------------------------|
| grpc.io/client/started_count                | started_count               | count        | client_method                |
| grpc.io/client/request_bytes                | request_bytes               | distribution | client_method                |
| grpc.io/client/response_bytes               | response_bytes              | distribution | client_method                |
| grpc.io/client/latency                      | latency                     | distribution | client_method                |
| grpc.io/client/finished_count               | latency                     | count        | client_method, client_status |

### Extra views

The following set of views are considered useful but not mandatory to monitor client side performance:

| View name                                   | Measure suffix              | Aggregation  | Tags suffix                  |
|---------------------------------------------|-----------------------------|--------------|------------------------------|
| grpc.io/client/request_messages_count       | request_messages_count      | distribution | client_method                |
| grpc.io/client/uncompressed_request_bytes   | uncompressed_request_bytes  | distribution | client_method                |
| grpc.io/client/response_messages_count      | response_messages_count     | distribution | client_method                |
| grpc.io/client/uncompressed_response_bytes  | uncompressed_response_bytes | distribution | client_method                |
| grpc.io/client/server_latency               | server_latency              | distribution | client_method                |

## Server

Server stats are recorded for each individual gRPC request. All measurements except `grpc.io/server/started_count` must be recorded at the end of each gRPC request.

| Measure name                               | Unit | Description                                                                                   |
|--------------------------------------------|------|-----------------------------------------------------------------------------------------------|
| grpc.io/server/started_count               | 1    | Number of all server requests started. Records "1" at the beginning of each gRPC request.     |
| grpc.io/server/request_messages_count      | 1    | Number of request messages received in a gRPC request. Has value "1" for non-streaming RPCs.  |
| grpc.io/server/request_bytes               | By   | Total compressed bytes received in total across all request messages in a gRPC request.       |
| grpc.io/server/uncompressed_request_bytes  | By   | Total uncompressed bytes received in total across all request messages in a gRPC request.     |
| grpc.io/server/response_messages_count     | 1    | Number of response messages sent  in a gRPC request. Has value "1" for non-streaming RPCs.    |
| grpc.io/server/response_bytes              | By   | Total compressed bytes sent in total across all response messages in a gRPC request.          |
| grpc.io/server/uncompressed_response_bytes | By   | Total uncompressed bytes sent in total across all response messages in a gRPC request.        |
| grpc.io/server/latency                     | ms   | Time between first byte of request received to last byte of response sent, or terminal error. |

### Tags

All server metrics should be tagged with the following. Some tags are not available for measurements recorded at the beginning of the gRPC request (e.g. `grpc.canonical_status`).

| Tag name           | Description                                                   |
|--------------------|---------------------------------------------------------------|
| grpc.server_method | Full gRPC method name, including package, service and method. |
| grpc.server_status | gRPC server status code.                                      |

### Default views

The following set of views are considered minimum required to monitor server side performance:

| View name                                   | Measure suffix              | Aggregation  | Tags suffix                  |
|---------------------------------------------|-----------------------------|--------------|------------------------------|
| grpc.io/server/started_count                | started_count               | count        | server_method                |
| grpc.io/server/request_bytes                | request_bytes               | distribution | server_method                |
| grpc.io/server/response_bytes               | response_bytes              | distribution | server_method                |
| grpc.io/server/latency                      | latency                     | distribution | server_method                |
| grpc.io/server/finished_count               | latency                     | count        | server_method, server_status |

### Extra views

The following set of views are considered useful but not mandatory to monitor server side performance:

| View name                                   | Measure suffix              | Aggregation  | Tags suffix                  |
|---------------------------------------------|-----------------------------|--------------|------------------------------|
| grpc.io/server/request_messages_count       | request_messages_count      | distribution | server_method                |
| grpc.io/server/uncompressed_request_bytes   | uncompressed_request_bytes  | distribution | server_method                |
| grpc.io/server/response_messages_count      | response_messages_count     | distribution | server_method                |
| grpc.io/server/uncompressed_response_bytes  | uncompressed_response_bytes | distribution | server_method                |

## FAQ

### Why different tag name for server/client method?
This way users can configure views to correlate incoming with outgoing requests. A view example:

| View name                                   | Measure suffix              | Aggregation  | Tags suffix                  |
|---------------------------------------------|-----------------------------|--------------|------------------------------|
| grpc.io/client/latency_by_server_method     | latency                     | distribution | client_method, server_method |
