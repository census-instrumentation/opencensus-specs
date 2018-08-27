# Resource API Overview
The resource library primarily defines a type that captures information about the entity
for which stats or traces are recorded. It further provides a framework for detection of
resource information from the environment and progressive population as signals propagate
from the core instrumentation library to a backend's exporter.

## Resource type
A `Resource` describes the entity for which a signal was collected through two fields:
* `type`: a string which describes a well-known type of entity. It SHOULD be namespaced
to avoid collisions across different environment, e.g. `k8s.io/container`,
`cloud.google.com/gce/instance`. 
* `labels`: a dictionary of labels with string keys and values that provide information
about the entity.

Type strings and label keys MAY start with a domain name and MAY be further namespaced through
single slashes. All other characters MUST be alphanumeric. Label values MAY contain any valid
unicode character.

Implementations MAY define a `Resource` data type, constructed from the parameters above.
`Resource` MUST have getters for retrieving all the information used in `Resource` definition.

Example in Go:
```go
type Resource {
	Type   string
	Labels map[string]string
}
```

## Populating resources
Resource information MAY be populated at any point between startup of the instrumented
application and passing it to a backend-specific exporter. This explicitly includes
the path through future OpenCensus components such as agents or services.

For example, process-identifying information may be populated through the library while
an agent attaches further labels about the underlying VM, the cluster, or geo-location.

### From environment variables
Population of resource information from environment varibales MUST be provided by the
core library. It provides the user with an ubiquitious way to manually provide information
that may not be detectable automatically through available integration libraries.

Two environment varibales are used:
* `OC_RESOURCE_TYPE`: defines the resource type. Leading and trailing whitespaces are trimmed.
* `OC_RESOURCE_LABELS`: defines resource labels as a comma-seperated list of key/value pairs.
The key MUST be seperated from the value by a single `=`. Values MAY be quoted with a single
leading and trailing `"`. If a values contains whitespaces, `=`, or `"` characters it MUST be
quoted and `"` characters MUST be escaped.

For example:
* `OC_RESOURCE_TYPE=k8s.io/container`
* `OC_RESOURCE_LABELS=k8s.io/pod_name="pod-xyz-123",k8s.io/container_name="c1",k8s.io/namespace="default"`

Population from environment variables MUST be the first applied detection process unless
the user explicitly overwrites this behavior.

### Auto-detection
Auto-detection of resource information in specific environments, e.g. specific cloud
vendors, MUST be implemented outside of the core libraries in third party or
[census-ecosystem][census-ecosystem] repositories.

### Merging
When new information is gained about a resource, already set labels or type fields MUST NOT
be overwritten. Label key namespaceing SHOULD be used to prevent collisions across different
resource detection steps.

### Detectors
To make auto-detection implementations easy to use, the core resource package SHOULD define
an interface to retrieve resource information. Additionally, helper functionality MAY be
provided to effectively make use of this interface.
The exact shape of those interfaces and helpers SHOULD be idiomatic to the respective language.

Example in Go:

```go
type Detector func(context.Context) (*Resource, error)

// Returns a detector that always returns a specific resource.
func NewDetectorFromResource(*Resource) Detector

// Returns a detector that runs all input detectors sequentially and merges their results.
func ChainedDetector(...Detector) Detector
```

## Exporter translation
A resource object MUST NOT be mutated further once it is passed to a backend-specific exporter.
From the provided resource information, the exporter MAY transform, drop, or add information
to build the resource identifying data type specific to its backend.
If the passed resource does not contain sufficient information, an exporter MAY drop
signal data entirely, if no sufficient resource information is provided to perform a correct
write.

For example, from a resource object

```javascript
{
	"type": "k8s.io/container",
	"labels": {
		// Populated from VM environment through auto-detection library.
		"cloud.google.com/gce/instance_id": "instance1",
		"cloud.google.com/zone": "eu-west2-a",
		"cloud.google.com/project_id": "project1",
		"cloud.google.com/gce/attributes/cluster_name": "cluster1",
		// Populated through OpenCensus resource environment varibales.
		"k8s.io/namespace": "ns1",
		"k8s.io/pod_name": "pod1",
		"k8s.io/container_name": "container1",
	},
}
```

an exporter for Stackdriver would create the following "monitored resource", which is a
resource type with well-known identifiers specific to its API:

```javascript
{
	"type": "k8s_container",
	"labels": {
		"project_id": "project1",
		"location": "eu-west2-a",
		"cluster_name": "cluster1",
		"namespace_name": "ns1",
		"pod_name": "pod1",
		"container_name": "container1",
	},
}
```

Exporter libraries MAY provide a default translation for well-known input resource types and labels.
Those would generally be based on community-supported detection integrations maintained in the
[census-ecosystem][census-ecosystem] organisation.

Additionally, exporters SHOULD provide configuration hooks for users to provide their own
translation unless the exporter's backend does not support resources at all. For such backends,
exporters SHOULD allow attaching converting resource labels to metric tags.

[census-ecosystem]: https://github.com/census-ecosystem
