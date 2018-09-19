Exemplars
===

Exemplars are example data points for aggregated data. In particular, for distribution-type
metrics, exemplars are points associated with each bucket in the distribution giving an
example of what was aggregated into the bucket.

Exemplars consist of a value that was recorded, an exact timestamp of when that value
was recorded, and a set of attachments.

Attachments
---

Exemplars my have zero or more attachments. Attachments are key-value pairs that
describe the context in which the exemplar was recored.

Well-known attachment keys
---

We define the following types of attachments:

1. `TraceID`: lower-case base16 encoded trace ID
2. `SpanID`:  lower-case base16 encoded span ID
3. `TraceOptions`: lower-case base16 encoded trace options
4. `Tag:KEY`: tags that were active at the time of recording, regardless of whether
   they were ultimately added to views or not. `KEY` is the tag key, the value
   of the attachment is the tag value in effect.

If `TraceID` and `SpanID` are provided, the associated trace should be sampled.

Retention in views
---

Libraries should attempt to retain and export at least once exemplar per bucket per reporting
period for distribution-type views. Libraries should not retain an unbounded number
of exemplars.

Libraries should prefer retaining exemplars with
[sampled traces](https://github.com/census-instrumentation/opencensus-specs/blob/master/trace/Sampling.md)
associated with them.
After considering associated trace, libraries should prefer exemplars with more attachments
over exemplars with fewer or no attachments.

Exporting
---

The interpretation of attachments is up to the exporter. Currently, only Stackdriver is known
to support exemplars.
