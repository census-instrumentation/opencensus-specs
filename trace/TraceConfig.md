# TraceConfig

Global configuration of the trace service. This allows users to change configs for the default
sampler, maximum events to be kept, etc.

## TraceParams
Represents the set of parameters that users can control 
* Default `Sampler` - used when creating a Span if no specific sampler is given. The default sampler
is a [Probability](Sampling.md) sampler with the probability set to `1/10000`.
* Default max number of `Attribute`s per `Span` - used when creating a Span if no specific limit on 
`Attribute`s is given. The default limit is 32.
* Default max number of `Annotation`s per `Span` - used when creating a Span if no specific limit on 
`Annotation`s is given. The default limit is 32.
* Default max number of `Message Event`s per `Span` - used when creating a Span if no specific limit 
on `Message Event`s is given. The default limit is 128.
* Default max number of `Link`s per `Span` - used when creating a Span if no specific limit on 
`Link`s is given. The default limit is 128.

## API Summary
* Permanently update the active TraceParams.
* Temporary update the active TraceParams. This API allows changing of the active params for a 
certain period of time. No more than one active temporary update can exist at any moment.
* Get current active TraceParams.
