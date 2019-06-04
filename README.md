# OpenCensus Specs

This is a high level design of the OpenCensus library, some of the API examples may be written
(linked) in C++, Java or Go but every language should translate/modify them based on language
specific patterns/idioms. Our goal is that all libraries have a consistent "look and feel".

This repository uses terminology (MUST, SHOULD, etc) from [RFC 2119][RFC2119].

## Overview

Today, distributed tracing systems and stats collection tend to use unique protocols and
specifications for propagating context and sending diagnostic data to backend processing systems.
This is true amongst all the large vendors, and we aim to provide a reliable implementation in
service of frameworks and agents. We do that by standardizing APIs and data models.

OpenCensus provides a tested set of application performance management (APM) libraries, such as
Metrics and Tracing, under a friendly OSS license. We acknowledge the polyglot nature of modern
applications, and provide implementations in all main programming languages including C/C++,
Java, Go, Ruby, PHP, Python, C#, Node.js, Objective-C and Erlang.

## Ecosystem Design

![Ecosystem layers][EcosystemLayers]

### Layers

#### Service exporters

Each backend service SHOULD implement this API to export data to their services.

#### OpenCensus library

This is what the following section of this document is defining and explaining.

#### Manually instrumented frameworks

We are going to instrument some of the most popular frameworks for each language using the
OpenCensus library to allow users to get traces/stats when they use these frameworks.

#### Tools for automatic instrumentation

Some of the languages may support libraries for automatic instrumentation. For example, Java
applications can use byte-code manipulation (monkey patching) to provide an agent that
automatically instruments an application. Note: not all the languages support this.

#### Application

This is customer's application/binary.

## Library Design

### Namespace and Package

* For details about the library package names structure see [Namespace and Package](NamespaceAndPackage.md).

### Components

This section focuses on the important components that each OpenCensus library must have to
support all required functionalities.

Here is a layering structure of the proposed OpenCensus library:

![Library components][LibraryComponents]

#### Context
Some of the features for distributed tracing and tagging need a way to propagate a specific 
context (trace, tags) in-process (possibly between threads) and between function calls.

The key elements of the context support are:

* Every implementation MUST offer an explicit or implicit generic Context propagation mechanism
  that allows different sub-contexts to be propagated.
* Languages that already have this support, like Go ([context.Context][goContext]) or C# 
([Activity][activity]), MUST use the language supported generic context instead of building their
own.
* For an explicit generic context implementation you can look at the Java
[io.grpc.Context][gRPCContext].

#### Trace

Trace component is designed to support distributed tracing (see the [Dapper paper][DapperPaper]).
OpenCensus allows functionality beyond data collection and export. For example, it allows
tracking of the active spans and keeping local samples for interesting requests.

The key elements of the API can be broken down as:

* A Span represents a single operation within a trace. Spans can be nested to form a trace tree.
* Libraries must allow users to record tracing events for a span (attributes, annotations, links,
  etc.).
* Spans are carried in the Context. Libraries MUST provide a way of getting, manipulating,
  and replacing the Span in the current context.
* Libraries SHOULD provide a means of dynamically controlling the trace global configuration at runtime
  (e.g. trace sampling rate/probability).
* Libraries SHOULD keep track of active spans and in memory samples based on latency/errors and
  offer ways to access the data.
* Because context must also be propagated across processes, library MUST offer the functionality
  that allows any transport (e.g RPC, HTTP, etc.) systems to encode/decode the “trace context” for
  placement on the wire.

##### Links

* Details about Trace package can be found [here](trace/README.md).
* Data model is defined at the [Trace Data Model][TraceDataModel].

#### Tags

Tags are values propagated through the Context subsystem inside a process and among processes by
any transport (e.g RPC, HTTP, etc.). For example tags are used by the Stats component to break
down measurements by arbitrary metadata set in the current process or propagated from a remote
caller.

The key elements of the Tags component are:

* A tag: this is a key-value pair, where the keys and values are strings. The API allows for
  creating, modifying and querying objects representing a tag value.
* A set of tags (with unique keys) carried in the Context. Libraries MUST provide a means
  of manipulating the tags in the context, including adding new tags, replacing tag values, deleting
  tags, and querying the current value for a given tag key.
* Because tags must also be propagated across processes, library MUST offer the functionality that
  allows RPC systems to encode/decode the set of tags for placement on the wire.

##### Links

* Details about Tags package can be found [here](tags/README.md).

#### Stats

The Stats component is designed to record measurements, dynamically break them down by
application-defined tags, and aggregate those measurements in user-defined ways. It is designed
to offer multiple types of aggregation (e.g. distributions) and be efficient (all measurement
processing is done as a background activity); aggregating data enables reducing the overhead of
uploading data, while also allowing applications direct access to stats.

The key elements the API MUST provide are:

* Defining what is to be measured (the types of data collected, and their meaning), and how data
  will be aggregated (e.g. into a distribution, cumulative aggregation vs. deltas, etc.). Libraries
  must offer ways for customer to define Metrics that make sense for their application, and support
  a canonical set for RPC/HTTP systems.
* Recording data - APIs for recording measured values. The recorded data is then broken down by tags
  carried in the context (e.g. a tag can have a value that describes the current RPC service/method
  name; when RPC latency is recorded, this can be made in a generic call, without having to specify
  the exact method), and aggregated as needed (e.g. a histogram of all latency values).
* Accessing the aggregated data. This can be filtered by data type, resource name, etc. This
  allows applications to easily get access to their own data in-process.

##### Links

* Details about Stats package can be found [here](stats/README.md).
* Data model is defined at the [Stats Data Model][StatsDataModel].

### Supported propagation formats

* Library MUST support the [W3C Distributed Tracing][DistributedTracingSpecs] format for Trace and Tags components.
* Binary encoding is defined at the [BinaryEncoding](encodings/BinaryEncoding.md) document.

[EcosystemLayers]: /drawings/EcosystemLayers.png "Ecosystem Layer"
[DapperPaper]: https://research.google.com/pubs/pub36356.html
[goContext]: https://golang.org/pkg/context
[gRPCContext]: https://github.com/grpc/grpc-java/blob/master/context/src/main/java/io/grpc/Context.java
[LibraryComponents]: /drawings/LibraryComponents.png "OpenCensus Library Components"
[RFC2119]: https://www.ietf.org/rfc/rfc2119.txt
[StatsDataModel]: https://github.com/census-instrumentation/opencensus-proto/blob/master/src/opencensus/proto/stats/v1/stats.proto
[DistributedTracingSpecs]: https://github.com/w3c/distributed-tracing
[TraceDataModel]: https://github.com/census-instrumentation/opencensus-proto/blob/master/src/opencensus/proto/trace/v1/trace.proto
[activity]: https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md
