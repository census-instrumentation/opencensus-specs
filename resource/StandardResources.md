# Standard Resources

This page lists the standard resource types in OpenCensus.

## Kubernetes (k8s)

Resources specific to Kubernetes:

### `k8s.io/container`
**Description:** A Kubernetes container instance.

**Labels:**
* `k8s.io/cluster/name`: The name of the cluster that the container is running in.
* `k8s.io/namespace/name`: The name of the namespace that the container is running in.
* `k8s.io/pod/name`: The name of the pod that the container is running in.
* `k8s.io/container/name`: The name of the container.

## Google Cloud

Resources specific to Google Cloud:

### `cloud.google.com/gce/instance`
**Description:** A VM instance in Amazon EC2.

**Labels:**
* `cloud.google.com/gce/project_id`: The GCP account number for the instance.
* `cloud.google.com/gce/zone`: The GCE zone in which the VM is running.
* `cloud.google.com/gce/instance_id`: The numeric VM instance identifier assigned by GCE.

## Amazon Web Services (AWS)

Resources specific to AWS:

### `aws.com/ec2/instance`
**Description:** A VM instance in Amazon EC2.

**Labels:**
* `aws.com/ec2/account_id`: The AWS account number for the VM.
* `aws.com/ec2/region`: The AWS region for the VM.
* `aws.com/ec2/instance_id`: The VM instance identifier assigned by AWS.
