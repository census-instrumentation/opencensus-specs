# Summary
A `Tag` is used to label anything that is associated
with a specific operation, such as an HTTP request. These `Tag`s are used to
aggregate measurements in a [`View`](https://github.com/census-instrumentation/opencensus-specs/blob/master/stats/DataAggregation.md#view) 
according to unique value of the `Tag`s. The `Tag`s can also be used to filter (include/exclude)
measurements in a `View`. `Tag`s can further be used for logging and tracing.

# Tag
A `Tag` consists of TagScope, TagKey, and TagValue.

## TagKey

`TagKey` is the name of the Tag. TagKey along with `TagValue` is used to aggregate
and group stats, annotate traces and logs.

**Restrictions**
- Must contain only printable ASCII (codes between 32 and 126 inclusive with below Exception)
- - Exceptions '=', ','
- Must have length greater than zero and less than 256 combined with the source and metadata.
- Must not be empty.

## TagValue

`TagValue` is a string or string wrapper. It MUST contain only printable ASCII (codes between
32 and 126)

## TagScope

`TagScope` is used to determine the scope of a `Tag`. For now, it has two possible values,
Local or Non-Local. In future, additional values be added to address specific situations.

The tag creator determines the scope of the tag.

**Local Scope**
Tag with `Local` scope are used within the process it created. Such tags are not propagated
across process boundaries. Even if the process is reentrant the tag MUST be excluded from
propagation when the call leaves the process.

**Non-Local Scope**
If a tag is created with the `Non-Local` scope then it is propagated across process boundaries subject 
to outgoing and incoming (on remote side) filter criteria. See `TagFilter` in [Tag Propagation](#Tag Propagation).

# TagSet 
`TagSet` is an abstract data type that represents collection of tags. 
i.e., each key is associated with exactly one value, but multiple keys can be
associated with the same value.  `TagSet` is serializable, and it represents
all of the information that could be propagated inside the process and across process boundaries.  
`TagSet` is a recommended name but languages can have more language specific name.

## Limits
Combined size of all `Tag`s should not exceed 8192 bytes before encoding.
The size restriction applies to the deserialized tags so that the set of decoded
 `TagSet`s is independent of the encoding format.

## TagSet Propagation
`TagSet` may be propagated across process boundaries or across any arbitrary boundaries for various 
reasons. For example, one may propagate 'ProjectId' Tag across all micro-services to break down metrics
by 'ProjectId'. Not all `Tag`s in a `TagSet` should be propagated and not all `Tag`s in a `TagSet`
should be accepted from a remote peer. Hence, `TagSet` propagator must allow specifying one or more
optional `TagFilter` for receiving `Tag`s or for forwarding `Tag`s or for both. `TagFilter`s for receiving MAY be
different then that for forwarding.

If no filter is specified for receiving then all `Tag`s are received. 
If no filter is specified for forwarding then all `Tag`s are forwarded except those that have `Local Scope`.

### TagFilter
Tag Filter consists of `TagFilterType` and `TagFilterRegexp`.

#### TagFilterType
There are three types of Filter

**Exclude**
If the `TagFilterType` is Exclude then any `Tag`s whose `TagKey` matches the `TagFilterRegexp`
MUST be excluded. Unmatched `Tag`s are subjected to further processing against remaining `TagFilter`s.

**Include**
If the `TagFilterType` is Include then any `Tag`s whose `TagKey` matches the `TagFilterRegexp`
MUST be included. Unmatched `Tag`s are subjected to further processing against remaining `TagFilter`s.


## Encoding

### Wire Format
TBD:

#### Over gRPC
TagSet should be encoded using [BinaryEncoding](https://github.com/census-instrumentation/opencensus-specs/tree/master/encodings)
and propagated using gRPC metadata opencensus-tag-bin.
TBD: how and if `TagScope` should be propagated.

#### Over HTTP

TBD: W3C [correlation context](https://github.com/w3c/correlation-context/blob/master/correlation_context/HTTP_HEADER_FORMAT.md)
may be an appropriate choice.


TBD: how and if `TagScope` should be propagated.
                                                
### Error handling
- Call should continue irrespective of any error related to encoding/decoding.
- There are no partial failures for encoding or decoding. The result of encoding or decoding
  should always be a complete `TagSet` or an error.  The type of error
  reporting depends on the language.
- Serialization should result in an error if the `TagSet` does not meet the
  size restriction above.
- Deserialization should result in an error if the serialized `TagSet`
  - cannot be parsed.
  - contains a `TagKey` or `TagValue` that does not meet the restrictions above.
  - does not meet the size restriction above.
