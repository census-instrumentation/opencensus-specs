# Namespace and Package names

## Introduction
This document describes the key package names (namespaces, etc.).
This is only a guideline, language implementations should
consider the best option to be idiomatic.

## Structure
The top level package name MUST be **opencensus** (for URL model MUST use **io.opencensus**).

The second level package names SHOULD be:
* **stats**: for all measurement related functionality
* **tags**: for all tagging related functionality
* **context**: for all context related functionality
* **trace**: for all trace functionality
* **common**: for all public API components that are shared across multiple of the above^*^. These are
typically utility classes such as timestamps, etc.
* **internal**: If required, "internal" subdirectories/names of each of the above SHOULD be used
for all internal API components. e.g. common internal can have utility classes such as providers,
string manipulation, etc.

(^*^): For Go, consider using the top-level package instead.
