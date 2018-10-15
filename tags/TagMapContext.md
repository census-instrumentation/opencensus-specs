# Summary
A `Tag` is a key-value pair that can be used to label anything that is associated
with a specific operation, such as an HTTP request. These `Tag`s are used to
aggregate measurements in a [`View`](https://github.com/census-instrumentation/opencensus-specs/blob/master/stats/DataAggregation.md#view) 
according to unique value of the `Tag`s. The `Tag`s can also be used to filter (include/exclude)
measurements in a `View`.

# TagMap 
`TagMap` is an abstract data type that represents collection of tags. 
i.e., each key is associated with exactly one value, but multiple keys can be
associated with the same value.  `TagMap` is serializable, and it represents
all of the information that could be propagated across process boundaries.

## TagKey

A string or string wrapper, that contains two parts, scope and key. The scope
of a tag is used to determine if a tag should be propagated or not.


**TagKey Syntax:**  
Scope and key are separated by ':'.
```
<Scope>:<Key>
```

**Scope**
Scope of a `TagKey` defines a boundary within which the `TagKey` is relevant. For example 
a `TagKey` http_route on frontend service could have a scope that spans across multiple services
that contributes to fulfilling the http request. On the other hand if you are measuring latency
of key individual functions in a ProductCatalog service then the scope of a `TagKey` func_name
is most likely limited to a process in ProductCatalog service.

The scope is used to determine whether a Tag should be propagated or not.

**Key**   
Key is a string, with following restrictions:

- Must contain only printable ASCII (codes between 32 and 126 inclusive with below Exception)
- - Exceptions ':', ','
- Must have length greater than zero and less than 256 combined with the scope.
- Must not be empty.

### Scope

#### Scope Syntax

```
<scope-type1-code>,<scope-value1>,<scope-type2-code>,<scope-value2>,...
```
- Scope MUST always be in a Type-Value pair.

There are following scopes types.

#### Scope Types
Scope Type is represented using code to reduce the size.

| Code | Type  |
|------|-------|
| 0    | Process |
| 1    | Internal-Service|
| 2    | External-Service|


##### Process
A `TagKey` with the scope type *Process* is relevant only to stats collected by the process.
Hence it should not be propagated across different processes. This is the default scope. If no
scope is present in the `Tagkey` then it is treated as of Process scope type.

##### Internal-Service
A `TagKey` with the scope type *Internal-Service* is relevant to stats collected within an
internal service which could be a single process or collection of process fulfilling the service.
An internal services is the one that is not accessible from the public domain.
An Example of internal service is a distributed caching service.

A `TagKey` with this scope is propagated between different processes that makes up the internal
service.


##### External-Service
A `TagKey` with *External Service* scope is stats collected across multiple 
internal services that fulfills the external service which is accessible from another
administrative domain.
 
A `TagKey` with this scope is propagated between internal services.

#### Example

```
TagKey1 = 2,RetailSvc:http_route
TagValue1 = /cart/checkout

TagKey2 = 1,CheckoutSvc:grpc_method
TagValue2 = PlaceOrder

TagKey3 = 0,ProductCatagalog:func_name
TagValue3 = ParseCatalog

```

## TagValue

`TagValue` is a string or string wrapper. It MUST contain only printable ASCII (codes between
32 and 126)


## Limits
Combined size of all `TagKey`s and `TagValue`s should not exceed 8192 bytes before encoding.
The size restriction applies to the deserialized tags so that the set of decoded
 `TagMap`s is independent of the encoding format.


# Propagation

`Tag`s are propagated based on their scope. By default implementation should neither receive
incoming `Tag`s nor it should forward any `Tag`s unless explicitly configured by the user.
It MUST not receive or forward any `Tag` without the scope.

## Recieving  
**TBD**: how to sepcify receive criteria?

## Forwarding
**TBD**: how to specify forward criteria?

## Encoding

### Over gRPC
TagMap should be encoded using [BinaryEncoding](https://github.com/census-instrumentation/opencensus-specs/tree/master/encodings)
and propagated using gRPC metadata opencensus-tag-bin.

### Over HTTP

**TBD**
                                                
## Error handling

- There are no partial failures.  The result of encoding or decoding
  should always be a complete `TagMap` or an error.  The type of error
  reporting depends on the language.
- Serialization should result in an error if the `TagMap` does not meet the
  size restriction above.
- Deserialization should result in an error if the serialized `TagMap`
  - cannot be parsed.
  - contains a `TagKey` or `TagValue` that does not meet the restrictions above.
  - does not meet the size restriction above.
