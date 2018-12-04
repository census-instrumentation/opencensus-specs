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
- Must contain only printable ASCII (codes between 32 and 126 inclusive)
- Must have length greater than zero and less than 256.
- Must not be empty.

## TagValue

`TagValue` is a string. It MUST contain only printable ASCII (codes between
32 and 126)

## TagScope

`TagScope` is used to determine the scope of a `Tag`. The values for the `TagScope` are
Local or Request. In future, additional values can be added to address specific situations.

The tag creator determines the scope of the tag.

**Local Scope**
Tag with `Local` scope are used within the process it created. Such tags are not propagated
across process boundaries. Even if the process is reentrant the tag MUST be excluded from
propagation when the call leaves the process.

**Request Scope**
If a tag is created with the `Request` scope then it is propagated across process boundaries subject 
to outgoing and incoming (on remote side) filter criteria. See `TagPropagationFilter` in 
[Tag Propagation](#Tag Propagation). Typically 'Request' tags represents a request, processing
 of which may span multiple entities.

# TagMap 
`TagMap` is an abstract data type that represents collection of tags. 
i.e., each key is associated with exactly one value.  `TagMap` is serializable, and it represents
all of the information that could be propagated inside the process and across process boundaries.  
`TagMap` is a recommended name but languages can have more language specific name.

## Limits
Combined size of all `Tag`s should not exceed 8192 bytes before encoding.
The size restriction applies to the deserialized tags so that the set of decoded
 `TagMap`s is independent of the encoding format.

## TagMap Propagation
`TagMap` may be propagated across process boundaries or across any arbitrary boundaries for various 
reasons. For example, one may propagate 'project-id' Tag across all micro-services to break down metrics
by 'project-id'. Not all `Tag`s in a `TagMap` should be propagated and not all `Tag`s in a `TagMap`
should be accepted from a remote peer. Hence, `TagMap` propagator must allow specifying an optional
list of ordered `TagPropagationFilter`s for receiving `Tag`s or for forwarding `Tag`s or for both. 
A `TagPropagationFilter` list for receiving MAY be different then that for forwarding.

If no filter is specified for receiving then all `Tag`s are received. 
If no filter is specified for forwarding then all `Tag`s are forwarded except those that have `Local Scope`.

### TagPropagationFilter
Tag Propagation Filter consists of action (`TagFilterAction`) and condition (`TagFilterMatchOperator` and
`TagFilterMatchString`). A `TagKey` 
is evaluated against condition of each `TagPropagationFilter` in order. If the condition is evaluated
to true then action is taken according to `TagFilterAction` and filter processing is stopped.
If the condition is evaluated to false then the `TagKey` is processed against next `TagPropagationFilter`
in the ordered list. If none of the condition is evaluated to true then the default
action is **Exclude**.

#### TagFilterAction
There are two actions to apply to a Tag.

**Exclude**
If the `TagFilterAction` is Exclude then any `Tag` whose `TagKey` evaluates to true 
with the condition (`TagFitlerMatchOperator` and `TagFilterMatchString`)
MUST be excluded.

**Include**
If the `TagFilterAction` is Include then any `Tag` whose `TagKey` evaluates to true 
with the condition (`TagFitlerMatchOperator` and `TagFilterMatchString`)
MUST be included. 
  
#### TagFilterMatchOperator

| Operator | Description |
|----------|-------------|
| EQUAL | The condition is evaluated to true if `TagKey` is exactly same as `TagFilterMatchString` |
| NOTEQUAL | The condition is evaluated to true if `TagKey` is NOT exactly same as `TagFilterMatchString` |
| HAS_PREFIX | The condition is evaluated to true if `TagKey` begins with `TagFilterMatchString` |

#### TagFilterMatchString
It is a string to compare against TagKey using `TagFilterMatchOperator` in order to include or exclude a `Tag`.

## Encoding

### Wire Format
TBD:

#### Over gRPC
TagMap should be encoded using [BinaryEncoding](https://github.com/census-instrumentation/opencensus-specs/tree/master/encodings)
and propagated using gRPC metadata opencensus-tag-bin.

#### Over HTTP

TBD: W3C [correlation context](https://github.com/w3c/correlation-context/blob/master/correlation_context/HTTP_HEADER_FORMAT.md)
may be an appropriate choice.

                                                
### Error handling
- Call should continue irrespective of any error related to encoding/decoding.
- There are no partial failures for encoding or decoding. The result of encoding or decoding
  should always be a complete `TagMap` or an error.  The type of error
  reporting depends on the language.
- Serialization should result in an error if the `TagMap` does not meet the
  size restriction above.
- Deserialization should result in an error if the serialized `TagMap`
  - cannot be parsed.
  - contains a `TagKey` or `TagValue` that does not meet the restrictions above.
  - does not meet the size restriction above.
