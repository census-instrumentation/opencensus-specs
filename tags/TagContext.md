# Tag Context API

TODO(sebright): Add more detail to the Tag Context summary, including how it
relates to the current context.

`TagContext` is an abstract data type that represents a collection of tags.  A
`TagContext` can be used to label anything that is associated with a specific
operation, such as an HTTP request.  Each tag is composed of a key (`TagKey`),
and a value (`TagValue`).  A `TagContext` represents a map from keys to values,
i.e., each key is associated with exactly one value, but multiple keys can be
associated with the same value.  `TagContext` is serializable, and it represents
all of the information that must be propagated across process boundaries.

## TagKey

A TagKey consists of a Name and a Scope.

The Name of a TagKey is a string or string wrapper, with some restrictions:

- Must contain only printable ASCII (codes between 32 and 126, inclusive).
- Must have length greater than zero and less than 256.

Scope indicates the default propagation behavior. The Scope may be one of:

* Local: Tags with this key are local to the current process and are not
  propagated across process boundaries, but may be propagated within-process.
* Request: Tags with this key are scoped to the processing of this request,
  and are propagated across process boundaries.

Scope should usually be explicitly specified. In cases where this isn't 
convenient, the default scope should be Local.

## TagValue

A string or string wrapper with the same restrictions as `TagKey`, except that it
is allowed to be empty.

## Serialization

This section applies to all serialization formats.  See
https://github.com/census-instrumentation/opencensus-specs/tree/master/encodings
for specific formats.

- The serialization format must preserve the `TagKey`-`TagValue` mapping.
- A `TagContext` can only be serialized or deserialized if the combined size of
  its keys and values is at most 8192 characters (8192 bytes).  The size
  restriction applies to the deserialized tags so that the set of serializable
  `TagContext`s is independent of the serialization format.

### Error handling

- There are no partial failures.  The result of serialization or deserialization
  should always be a complete `TagContext` or an error.  The type of error
  reporting depends on the language.
- Serialization should result in an error if the `TagContext` does not meet the
  size restriction above.
- Deserialization should result in an error if the serialized `TagContext`
  - cannot be parsed.
  - contains a `TagKey` or `TagValue` that does not meet the restrictions above.
  - does not meet the size restriction above.
