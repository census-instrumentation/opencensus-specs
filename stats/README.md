# OpenCensus Library Stats Package
This documentation serves to document the "look and feel" of the open source stats package. It 
describes the key types and the overall behavior.

## Overview
OpenCensus allows users to create typed measures, record measurements, define data aggregation, and
export aggregated data.

### Main APIs
* [Record API](Record.md): used by the application and libraries (e.g gRPC) owners to record 
metrics.
* [Data Aggregation API](DataAggregation.md): used by the application owners to define and enable
data aggregation.
* [Export API](Export.md): used by the specific vendors to define vendor specific exporters (e.g.
Prometheus, Stackdriver, SignalFx).

## Utils
* [HTTP integration](HTTP.md): document about how to instrument http frameworks.
* [gRPC integration](gRPC.md): document about how to instrument gRPC framework.
