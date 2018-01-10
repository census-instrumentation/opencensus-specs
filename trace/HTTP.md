# Tracing HTTP

## Client

* Client span name format
* Message send events
* Message receive events
* Status
* Attributes names
** Host
** Resolved IP of connected socket
** Path
** Protocol version
* Transport layer details (how do we represent TLS negotiation)
* Redirects (how are these to be represented)
* Retries (should be logged as separate requests)
* https://blog.golang.org/http-tracing
* Interaction with nested protocols (gRPC)

## Server

* Server span name format
* Status representation
* Message send events
* Message receive events
