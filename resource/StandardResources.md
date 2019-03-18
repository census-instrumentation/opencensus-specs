# Standard Resources

This page lists the standard resource types in OpenCensus. For more details on how resources can 
be combined see [this](Resource.md).

OpenCensus defines these fields.
 * [Compute Unit](#compute-unit)
   * [Container](#container)
 * [Deployment Service](#deployment-service)
   * [Kubernetes](#kubernetes)
 * [Compute Instance](#compute-instance)
   * [Host](#host)
 * [Environment](#environment)
   * [Cloud](#cloud)
   * [Cluster](#cluster)

## TODOs
* Add logical compute units: Service, Task - instance running in a service.
* Add more compute units: Process, Lambda Function, AppEngine unit, etc.
* Decide if lower case strings only.
* Consider to add optional/required for each label.

## Compute Unit
Resources defining a compute unit (e.g. Container, Process, Lambda Function).

### Container
**type:** `container`

**Description:** A container instance. This resource can be [merged](Resource.md#Merging) with a
deployment service resource, a compute instance resource, and an environment resource.

| Label  | Description  | Example  |
|---|---|---|
| container.name | Container name. | `opencenus-autoconf` |
| container.image.name | Name of the image the container was built on. | `gcr.io/opencensus/operator` |
| container.image.tag | Container image tag. | `0.1` |

## Deployment Service
Resources defining a deployment service (e.g. Kubernetes).

### Kubernetes
**type:** `k8s`

**Description:** A Kubernetes pod resource. This resource can be [merged](Resource.md#Merging) with
a compute instance resource, and/or an environment resource.

| Label  | Description  | Example  |
|---|---|---|
| k8s.cluster.name | The name of the cluster that the container is running in. | `opencensus-cluster` |
| k8s.namespace.name | The name of the namespace that the container is running in. | `default` |
| k8s.pod.name | The name of the pod that the container is running in. | `opencensus-pod-autoconf` |

## Compute Instance
Resources defining a computing instance (e.g. host).

### Host
**type:** `host`

**Description:** A host is defined as a general computing instance. This resource should be
[merged](Resource.md#Merging) with an environment resource.


| Label  | Description  | Example  |
|---|---|---|
| host.hostname | Hostname of the host.<br/> It contains what the `hostname` command returns on the host machine. | `opencensus-test` |
| host.id | Unique host id.<br/> For Cloud this must be the instance_id assigned by the cloud provider | `opencensus-test` |
| host.name | Name of the host.<br/> It may contain what `hostname` returns on Unix systems, the fully qualified, or a name specified by the user. | `opencensus-test` |
| host.type | Type of host.<br/> For Cloud this must be the machine type.| `n1-standard-1` |

## Environment

Resources defining a running environment (e.g. Cloud, Data Center).

### Cloud
**type:** `cloud`

**Description:** A cloud infrastructure (e.g. GCP, Azure, AWS).

| Label  | Description  | Example  |
|---|---|---|
| cloud.provider | Name of the cloud provider.<br/> Example values are aws, azure, gcp. | `gcp` |
| cloud.account.id | The cloud account id used to identify different entities. | `opencensus` |
| cloud.availability_zone | Availability zone in which entities are running. | `us-central1` |
| cloud.zone | Region in which entities are running. | `us-central1-a` |

### Cluster
**type:** `cluster`

**Description:** Dedicated (on premise) group of machines (computer systems).

| Label  | Description  | Example  |
|---|---|---|
| cluster.name | Name of the cluster. | `my_opencensus_cluster` |
| cluster.location | Location of the cluster. | `us-east` |