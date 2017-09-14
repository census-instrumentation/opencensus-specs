# Namespace and Package names

## Introduction
This document describes the key package names (namespaces, etc.). This document uses terminology
(MUST, SHOULD, etc) from [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

## Structure
The top level package name MUST be **opencensus**. If an URL model is needed use **io.opencensus**.

The second level package names MUST be:
* **stats**: for all measurement related functionality
* **tags**: for all tagging related functionality
* **context**: for all context related functionality
* **trace**: for all trace functionality
* **common**: for all public API components that are shared across multiple of the above. These are
typically utility classes such as timestamps, etc.
* **internal**: If required, "internal" subdirectories/names of each of the above SHOULD be used
for all internal API components. e.g. common internal can have utility classes such as providers,
string manipulation, etc.