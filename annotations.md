# Annotations
Annotations are key-value pairs that are attached to spans. While developers are free to attach their own annotations to data captured in Census, it's valuable for common / popular metadata to share the same key names.

## General
These annotations should be present on all applicable spans

Annotation Name      |Description
---------------------|---
/rpc/request/size    |
/rpc/response/size   |
/http/method         |
/http/status_code    |
/grpc/status         |
/http/url            |
/http/host           |
/ip/v4               |
/ip/v6               |
/stacktrace          |
/http/client_city    |
/http/client_country |
/http/client_region  |
/sql/query_string    |
/agent_name          |
/agent_version       |

## Kubernetes
These annotations should be present on spans captured from Kubernetes applications

Annotation Name      |Description
---------------------|---
kube/cluster_name    |
kube/namespace_id    |
kube/pod_id          |
kube/container_name  |
kube/service         |
