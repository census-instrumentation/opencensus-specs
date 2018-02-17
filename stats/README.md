# OpenCensus Library Trace API
This document serves to document the "look and feel" of the open source stats API's. It describes
the key types (classes, structures, etc.) and functions (methods, etc.).

OpenCensus allows users to create typed measures, record measurements, define data aggregation, and
export aggregated data.

## Main APIs
* [Record API](Record.md): used by the application and third-party libraries (e.g gRPC) owners to
record metrics.
* [Data Aggregation API](DataAggregation.md): used by the application owners to define data
aggregation.
* [Export API](Export.md): used by the specific vendors to define vendor specific exporters (e.g.
Prometheus, Stackdriver, SignalFx).
