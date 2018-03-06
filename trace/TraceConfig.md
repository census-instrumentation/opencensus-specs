# TraceConfig

Global configuration of the trace service. This allows users to change configs for the default
sampler, maximum events to be kept, etc.

## TraceParams
Represents the set of parameters that users can control 
* Default `Sampler` - used when creating a Span if no specific sampler is given.

TODO(bdrutu): Define the rest of the params.

## API Summary
* Permanently update the active TraceParams.
* Temporary update the active TraceParams. This API allows changing of the active params for a 
certain period of time. No more than one active temporary update can exist at any moment.
* Get current active TraceParams.
