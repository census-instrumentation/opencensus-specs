# Monitored Resource

This document is about the monitored resource, what resources are supported and how to automatically
detect these resources.

## Supported Monitored Resources

* GCP_GCE_INSTANCE
  * gcp_account: The GCP account number for the instance.
  * instance_id: The numeric VM instance identifier assigned by GCE.
  * zone: The GCE zone in which the VM is running.
* GCP_GKE_CONTAINER
  * gcp_account: The GCP account number for the instance.
  * cluster_name: The name for the cluster the container is running in.
  * namespace_id: The identifier for the cluster namespace the container is running in.
  * instance_id: The identifier for the GCE instance the container is running in.
  * pod_id: The identifier for the pod the container is running in.
  * container_name: The name of the container.
  * zone: The zone for the instance.
* AWS_EC2_INSTANCE
  * aws_account: The AWS account number for the instance.
  * instance_id: The VM instance identifier assigned by AWS.
  * region: The AWS region for the cluster.
  
## How to automatically detect the Monitored Resource

The resources types and properties are detected using some Metadata services or environment 
variables:
* AWS Metadata Service documentation is [here][AWSMetadata].
* GCP Metadata Service documentation is [here][GCPMetadata].

Before implementing any util library that talks to any metadata service look for already 
implemented libraries, e.g. [see this][GCPMetadataJavaExmple] for Java GCP metadata service support.


### GCP_GKE_CONTAINER

If the environment variable `KUBERNETES_SERVICE_HOST` is set.

| Property Name  | Environment Variable | Metadata Request                 |
|----------------|----------------------|----------------------------------|
| gcp_account    |                      | project/project-id               |
| cluster_name   |                      | instance/attributes/cluster-name |
| namespace_id   | NAMESPACE            |                                  |
| instance_id    |                      | instance/id                      |
| pod_id         | HOSTNAME             |                                  |
| container_name | CONTAINER_NAME       |                                  |
| zone           |                      | instance/zone                    |

The namespace_id and container_name are optional. We cannot get their value from environment
variables unless k8s users expose them via the [Downward API][DownwardAPI]. See k8s
[documentation][K8SDocumentation] and [code sample][K8SCodeSample].

### GCP_GCE_INSTANCE

If the GCP metadata service returns a value for "instance/id" and not GCP_GKE_CONTAINER.

| Property Name  | Environment Variable | Metadata Request   |
|----------------|----------------------|--------------------|
| gcp_account    |                      | project/project-id |
| instance_id    |                      | instance/id        |
| zone           |                      | instance/zone      |

### AWS_EC2_INSTANCE
 
If the AWS metadata service returns a value for "instance_id".

| Property Name  | Environment Variable | Metadata Request           |
|----------------|----------------------|----------------------------|
| aws_account    |                      | instance-identity/document |
| instance_id    |                      | instance_id                |
| region         |                      | instance-identity/document |

The value return by the `instance-identity/document` metadata request is a document described 
[here][AWSMetadataIdentityDocument].

[AWSMetadata]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html#instancedata-data-retrieval
[AWSMetadataIdentityDocument]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-identity-documents.html
[DownwardAPI]: https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/
[GCPMetadata]: https://cloud.google.com/compute/docs/storing-retrieving-metadata
[GCPMetadataJavaExmple]: https://github.com/GoogleCloudPlatform/google-cloud-java/blob/master/google-cloud-core/src/main/java/com/google/cloud/MetadataConfig.java
[K8SDocumentation]: https://cloud.google.com/kubernetes-engine/docs/tutorials/custom-metrics-autoscaling#exporting_metrics_from_the_application
[K8SCodeSample]: https://github.com/GoogleCloudPlatform/kubernetes-engine-samples/blob/master/custom-metrics-autoscaling/direct-to-sd/sd_dummy_exporter.go
