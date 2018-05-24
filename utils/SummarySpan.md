# Objective
A request for a typical Service goes through a cluster of Load Balancers
before it is processed by the server. Sometimes there may be more stages in
between. Each stage introduces some processing delay. There is also a network
delay between these stages. These delays contribute to overall response time of
the request. 


From a perspective of a typical Service customer, a response time of a
query is the time elapsed between the first Load Balancers (LB) of the provider
receives the request and the time this LB sends the response back. If the LB is
co-located with the Server then the overall response time from LB is practically
the same as Server processing time (assuming LB’s processing time and network
delay between LB and the server is negligible). However, if LB is not co-located
then the problem could very well be on the Provider’s internal network between
LB and the Server. Providing visibility into response time from various vantage
points helps narrow down the problem for the customer and the provider. 

![Summary Span Overview][SummarySpanOverview]

The objective of this spec is to provide visibility into response time of the
request from one or more stages.

# Specification
For the purpose of this specification we will only have two stages for request
processing, Load Balancer and Server. However, it doesn’t restrict to extend it
for multiple stages. The specification includes latency and sampling bit but can
also be extended to support other measurements.

## Measurement Data
Two measurements that will be reported are lb_latency_ns and server_latency_ns.
Both fields are optional. For example, if the server is not instrumented or not
enabled to collect the measurement then the server_latency_ns will not be
reported. Similarly if LB is not instrumented or not enabled then lb_latency_ns
will not be reported.

![Summary Span Measurement Data][SummarySpanMeasurementData]

### Measuring for Non-Streaming Request
1. **lb_latency_ns**: It is the time elapsed between the time LB received the request
and the time it sends the response status (Tlb1 - Tlb0). It includes processing
time on LB + downstream network delay + server_latency_ns.
2. **server_latency_ns**: It is the time elapsed between the time Server received the
request and the time Server sends the response status (Ts1-Ts0)
3. **client_latency_ns**: It is the time elapsed between the request sent to LB and the
response received from the LB (Tc1-Tc0). It is also known as round-trip-latency.
This is measured on the client side and it is not reported. It is simply used
for the interpretation of the measurements (see below).
4. **trace_option**: The least significant bit of the trace_option will be used to
indicate if the request is sampled or not. Other bits in the trace_option are
reserved for future use.

### Measurement for Streaming RPC
**trace_option**: There is no difference compared to non-streaming RPC.

#### Case 1: Server Streaming
**server_latency_ns** = Time elapsed between request received on the server and the
response status. Response status is sent with the last chunk of the response
data or after last chunk of the response data.

**lb_latency_ns** = same as above but on LB.

#### Case 2: Client Streaming
**server_latency_ns** = Time elapsed between last chunk of the request received on
the server and the response status sent from the server.

**lb_latency_ns** = same as above but on LB.

#### Case 3: Both Streaming
**server_latency_ns** = Time elapsed between last chunk of the request received on
the server and the response status sent from the server.

#### Limitations
There are limitations measuring performance when streaming is used.
- In case 1, it would be hard to know if the server continued to process the
request while serving the response. In such case the above latency may not
provide sufficient information. It only works for the case when the server
processes the request and has the entire response ready to send it back to the
client but it sends that in multiple chunks (stream). Here the processing time
for the request is much larger than sending chunks of data on the wire.
- In case 2, the server may be able to start processing the request as soon as the
first chunk is received. We cannot know that without understating the
application and the content of the request.
- In case 3, combination of limitation in case 1 and 2 applies here. 


### How to interpret the latency measurement?

| client_latency_ns | lb_latency_ns | server_latency_ns | Interpretation |
|-------------------|---------------|-------------------|----------------|
| LOW |  LOW |  LOW | Normal. Services are running normally. |
| HIGH | HIGH | LOW | LB is taking longer to process or network between the LB and the Server is congested. |
| HIGH | HIGH | HIGH | Server is overloaded or some other underlying issue. Depending on the difference between the latency the problem could spread across. |
| HIGH | LOW | HIGH | Not possible.  |
| HIGH | LOW | LOW | There is a Network issue between the client and LB. |
| HIGH | MISSING | LOW | The issue is not on the server but one cannot conclusively determine if the issue is on LB, or in the network between the LB and the server, or In the network between the Client and the LB |
| HIGH | MISSING | HIGH | The issue is on the server. Additionally, there could be an issue in other segments. |
| HIGH | HIGH | MISSING | The issue is on LB or beyond but cannot conclusively determine if it is on the server or not. |


### How to use Sampling bit?
Sampling-bit is used to compare traces and measurements end-to-end. Customers
can use the sampling-bit information to log traces and measurements on its side.
The traces and measurement from both sides can then be used to isolate the
problem.  

### Encoding

The measurement data will be returned to the client in response trailer. The
encoding of the same is defined for two different transport methods, Rest API
using http and gRPC.


#### Encoding with gRPC
For gRPC census-server-stats-bin metadata will be sent in gRPC trailer. The
encoding of this metadata is as per the format defined [here](https://github.com/census-instrumentation/opencensus-specs/blob/master/encodings/BinaryEncoding.md). All data is encoded
in little-endian.

#### Census-server-stats-bin Encoding
Encoding is based on [BinaryEncoding](../encodings/BinaryEncoding.md)
```
version_id                = 0 (uint8),
server_latency_ns (id)    = 0 (uint8),
server_latency_ns (value) = x (int64),
lb_latency_ns     (id)    = 1 (uint8),
lb_latency_ns     (value) = y (int64),
trace-option      (id)    = 2 (uint8),
trace-option      (value) = z (int8)
   - bit 0 (mask 0x01) - Sampling bit. 
         1 = request is sampled
         0 = request is not sampled
   - bits 1-7 (mask 0xFE) - Reserved.
```

# API

## Data Model

```
// This package describes the  data model. It is currently experimental

class ServerStats {
   // Latency observed at server while processing the request.
   long serverLatencyNs;

   // Latency observed at Load Balancer while processing the request.
   long lbLatencyNs;

   // A bitmap of tracing options. 
   // Only least significant bit is used for now.
   // Sampling Bit (least significant bit).
   int traceOption;
}

```

## Interface
Each implementation should provide following
- Marshaller to encode/decode from gRPC metadata headers as per
  [this](#census-server-stats-bin-encoding)

[SummarySpanOverview]: /utils/drawings/SummarySpanOverview.png "Summary Span Overview"
[SummarySpanMeasurementData]: /utils/drawings/SummarySpanMeasurementData.png "Summary Span Measurement Data"

