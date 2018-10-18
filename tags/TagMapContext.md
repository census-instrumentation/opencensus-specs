# Summary
A `Tag` is a key-value pair that can be used to label anything that is associated
with a specific operation, such as an HTTP request. These `Tag`s are used to
aggregate measurements in a [`View`](https://github.com/census-instrumentation/opencensus-specs/blob/master/stats/DataAggregation.md#view) 
according to unique value of the `Tag`s. The `Tag`s can also be used to filter (include/exclude)
measurements in a `View`. `Tag`s can further be used for logging and tracing.

# TagMap 
`TagMap` is an abstract data type that represents collection of tags. 
i.e., each key is associated with exactly one value, but multiple keys can be
associated with the same value.  `TagMap` is serializable, and it represents
all of the information that could be propagated inside the process and across process boundaries.  
`TagMap` is a recommended name but languages can have more language specific name.

## TagKey

`TagKey` consists of source, metadata and name.

### Source

Source of a tag is either remote or local.
 
**Local Source**
If a tag is created within an entity it is considered local tag for the entity.
The entity here could be a process, a collection of process, a micro-service, or any arbitrary 
definition. When a tag is created it's source is always designated as a local tag.

**Remote Source**
If a tag is received from another entity it is considered as remote tag for the receiving entity.


#### Source Transition 
A receiver at the edge of an entity converts source of all incoming tags to remote tags. Once the
source of a tag is designated as remote it stays as remote for the rest of the request processing.
This is applicable to reentrant processes as well ie. A tag T is created in an entity E and goes
through process P as local tag. It then leaves entity E and 
returns back and goes through Process P again. Tag T is considered as remote tag by process P
second time. (This only matters if Process P differentiates between remote and local tags. For e.g
it ignores all remote tags and uses only local tags for aggregation. Note )


A tag propagator should provide an option to declare that it is running on the edge of an entity.

### Metadata

Metadata of a `TagKey` is used to provide additional information about the `TagKey`. The 
metadata can be used to filter tags. Tags are filtered on the edge of an entity. Metadata is 
simply a collection of an arbitraty string.

**Restrictions**
TBD

### TagKeyName
`TagKeyName` is the name of the TagKey. TagKeyName along with `TagValue` is used to aggregate
and group stats, annotate traces and logs.

**Restrictions**
- Must contain only printable ASCII (codes between 32 and 126 inclusive with below Exception)
- - Exceptions ':', ','
- Must have length greater than zero and less than 256 combined with the source and metadata.
- Must not be empty.

## TagValue

`TagValue` is a string or string wrapper. It MUST contain only printable ASCII (codes between
32 and 126)


## Limits
Combined size of all `TagKey`s and `TagValue`s should not exceed 8192 bytes before encoding.
The size restriction applies to the deserialized tags so that the set of decoded
 `TagMap`s is independent of the encoding format.

# Tag Creation
When a `Tag` is created, it's `Source` is designated as local. If there already exists a Tag with 
same `TagKeyName` then existing `Tag` is removed and new `Tag` is added.


# Tag Propagation
Tag propagation involves receiving and forwarding of `Tag`s. Forwarding is simple, a tag 
propagator MUST forward all tags associated with the request. This includes locally created and 
remote tags accepted at the Edge of an entity. Receiving is based on the location of receiver.

## Receiving At The Edge of an Entity

A propagator on the receiver at the edge of an entity determines if a tag should be accepted or 
dropped. The decision is made based on any combination of source, metadata and the `TagKeyName` 
of a `Tag`. A tag propagator should allow custom filter to process incoming `Tag`s. Default filter 
should drop all tags.

## Receiving within an Entity
A propagator should simply receive all tags.



# Encoding

## Wire Format
TBD

### Over gRPC
TagMap should be encoded using [BinaryEncoding](https://github.com/census-instrumentation/opencensus-specs/tree/master/encodings)
and propagated using gRPC metadata opencensus-tag-bin.

### Over HTTP

**TBD**
                                                
## Error handling
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
