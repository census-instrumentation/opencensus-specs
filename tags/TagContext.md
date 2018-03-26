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

A string or string wrapper, with some restrictions:

- Must contain only printable ASCII (codes between 32 and 126, inclusive).
- Must have length greater than zero and less than 256.

## TagValue

A TagValue can be any unicode string whose size when encoded in UTF-8
is less than 256 bytes. An empty value is allowed.

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
