# Standard Resources

This page lists the standard resource types in OpenCensus. For more details on how resources can 
be combined see [this](Resource.md)

ECS defines these fields.
 * [Compute Unit](#compute-unit)
   * [container](#container)
 * [Deployment Service](#deployment-service)
   * [Kubernetes](#kubernetes)
 * [Compute Instance](#compute-instance)
   * [Host](#host)
 * [Environment](#event)
   * [Cloud](#cloud)
   * [Cluster](#cluster)
   * [Data Center](#data-center)

## TODOs
* Add logical compute units: Service, Task - instance running in a service.
* Add more compute units: Process, Lambda Function, AppEngine unit, etc.

## Compute Unit
Resources defining a compute unit (e.g. Container, Process, Lambda Function).

### Container
**type:** `container`

**Description:** A container instance. This resource can be merged with a deployment service
resource, a compute instance resource and an environment resource.

| Label  | Description  | Example  |
|---|---|---|
| container.id | Unique container id. |  |
| container.image.name | Name of the image the container was built on. |  |
| container.image.tag | Container image tag. |  |
| container.name | Container name. |  |

## Deployment Service
Resources defining a deployment service (e.g. Kubernetes).

### Kubernetes
**type:** `k8s`

**Description:** A Kubernetes pod resource. This resource can be merged with a compute instance
resource and/or an environment resource.

**Labels:**
| Label  | Description  | Example  |
|---|---|---|
| k8s.cluster.name | The name of the cluster that the container is running in. |  |
| k8s.namespace.name | The name of the namespace that the container is running in. |  |
| k8s.pod.name | The name of the pod that the container is running in. |  |

## Compute Instance
Resources defining a computing instance (e.g. host).

### Host
**type:** `host`

**Description:** A host is defined as a general computing instance.

| Label  | Description  | Example  |
|---|---|---|
| host.hostname | Hostname of the host.<br/> |  |
| host.id | Unique host id.<br/> For Cloud this must be the instance_id assigned by the cloud provider | `i-1234567890abcdef0` |
| host.name | Hostname of the host. |  |
| host.ip | Host ip address. |  |
| host.type | Type of host.<br/> For Cloud this must be the machine type like `t2.medium`.|  |

This resource should be merged with an environment resource.

## Environment

Resources defining an running environment (e.g. Cloud, Data Center).

### Cloud
**type:** `cloud`

**Description:** A cloud or infrastructure (e.g. GCP, Azure, AWS).

| Label  | Description  | Example  |
|---|---|---|
| cloud.provider | Name of the cloud provider.<br/> Example values are aws, azure, gcp. | `gcp` |
| cloud.account.id | The cloud account id used to identify different entities. |  |
| cloud.availability_zone | Availability zone in which entities are running. | `us-east-1c` |
| cloud.region | Region in which entities are running. |  |

### Cluster
**type:** `cluster`

**Description:** Cluster is a sub-unit of a data center and should be merged with a `data_center`
resource.

| Label  | Description  | Example  |
|---|---|---|
| cluster.name | Name of the cluster, usually inside a data-center. |  |

### Data Center
**type:** `data_center`

**Description:** Dedicated (on premise) group of machines (computer systems).

| Label  | Description  | Example  |
|---|---|---|
| data_center.name | Name of the data center. |  |
| data_center.location | Location of the data center. |  |