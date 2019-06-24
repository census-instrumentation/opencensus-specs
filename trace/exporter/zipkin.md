# Zipkin Reporter

## OC Span to Zipkin JSON

### Annotations

An annotation in Zipkin data model is a timestamp and string value. OpenCensus annotations must be converted to string representations of the annotation or message event.

OpenCensus annotation is a timestamp and either a description and attributes:

```
{"time": integer,
  "annotation": {"description": string, 
                 "attributes": {string: attribute_value}}}
``` 

or a message event:

```
{"time": integer,
  "message_event": {"type": string, 
                    "id": integer,
                    "compressed_size": integer,
                    "uncompressed_size": integer}}
``` 

In this format the Zipkin annotation looks like:
                 
```
{"timestamp": integer,
 "value": string}
```

To not lose information the attributes of an OpenCensus annotation are encoded into a string of `key=value` pairs separated by commas and appended to the description string followed by the string `Attributes:`.

For example:

```
{"time": 12345,
  "annotation": {"description": "the annotation description", 
                 "attributes": {"key-1": "value-1",
                                "key-2": "value-2"}}}
``` 

Becomes:

```
{"timestamp": 12345,
 "value": "the annotation description Attributes:{key-1=value-1, key-2=value-2}"}
```

For a message event each element is similarly encoded into a `key=value` pair separated by a comma and appended to the string `MessageEvent:`.

For example:

```
{"time": 67890,
  "message_event": {"type": "SENT", 
                    "id": 5,
                    "compressed_size": 25,
                    "uncompressed_size": 38}}
``` 

Becomes:

```
{"timestamp": 67890,
 "value": "MessageEvent:{type=Sent, id=5, compressed_size=25, uncompressed_size=38}"}
```
