---
Author: Marcus Notø, Oskar Marthinussen
Title: Resource Management
Version: 1.0.0
externally-exposed: true
--- 

# Resource Management with OpenShift Tenants

## Overview

OpenShift, built on Kubernetes, offers a robust platform for deploying and managing containerized applications. A fundamental aspect of this management is the efficient allocation and utilization of resources such as CPU and memory. Proper resource management ensures that applications run smoothly without overconsuming resources, which could affect other applications in the cluster. 

## Managing Resource Allocation in Deployments
In OpenShift, each container within a pod can specify the amount of CPU and memory it requires (request) and the maximum amount it can consume (limit). 

- **Resource Request**: The amount of CPU or memory that OpenShift reserves on the node for the given pod. The scheduler uses this value to determine on which node to place the pod.
- **Resource Limit**: The maximum amount of CPU or memory that a container is allowed to use. If the container tries to exceed this limit, it may be throttled or terminated. ￼

Properly defining resource requests and limits helps maintain a balanced workload, preventing resource contention and ensuring fair allocation across applications.

## Tenant Resource Quotas

Each OpenShift tenant is assigned a ClusterResourceQuota, which defines the maximum amount of CPU and memory that can be requested across all namespaces belonging to that tenant. This ensures fair resource distribution and prevents any single tenant from consuming an excessive share of cluster resources.

### Request vs. Limit in ClusterResourceQuota

The resource request quota is a hard cap, meaning the total sum of all requested CPU and memory across all pods in all namespaces within the tenant cannot exceed this limit. This prevents tenants from over-provisioning resources beyond their allocated quota.

The ClusterResourceQuota also supports defining limits, but it is generally recommended that developers set limits at the pod level rather than within the quota. While requests define the minimum reserved resources, limits control the maximum resources a pod can consume. Setting limits at the pod level ensures that individual workloads do not overconsume resources, while the ClusterResourceQuota primarily helps manage overall tenant resource usage across multiple namespaces. This approach allows for greater flexibility and prevents unintended resource exhaustion within a tenant’s quota.

### Why Use Request-Based Quotas?

Quotas based on requests ensure that tenants only reserve the resources they need, preventing inefficient overcommitment. However, requests do not restrict actual usage. Pods can consume more than their requests if additional capacity is available. This is why limits should be set at the pod level to prevent excessive resource consumption.

To maintain efficient resource utilization across the cluster, requests should reflect actual resource needs under normal workload conditions, limits should be properly defined at the pod level, and quotas should be regularly monitored and adjusted as needed. By managing requests in ClusterResourceQuota and setting limits within pods, tenants can balance flexibility with stability, ensuring fair and efficient resource distribution in OpenShift.


## Scaling Workloads in OpenShift

In OpenShift, workloads often experience fluctuating demands, requiring scaling to maintain performance and optimize resource utilization. OpenShift provides automatic scaling mechanisms, such as Horizontal Pod Autoscaling (HPA) and Vertical Pod Autoscaling (VPA), which adjust workloads based on real-time resource consumption.

### Horizontal Pod Autoscaling (HPA)

HPA automatically adjusts the number of pod replicas based on observed CPU utilization or other select metrics. This ensures that applications can scale out during high demand and scale in during low demand, optimizing resource usage.

### Vertical Pod Autoscaling (VPA)

VPA automatically adjusts CPU and memory requests for pods based on their actual usage, aiming to optimize resource allocation without overprovisioning. Unlike static resource requests, VPA modifies these values dynamically and restarts pods when necessary to apply changes.

### HPA and VPA in OpenShift Tenants

Automated scaling can be a useful practice for managing fluctuating workloads. By dynamically adjusting resources, applications can handle variable demand without requiring manual intervention. HPA ensures that workloads can scale horizontally, distributing load efficiently, while VPA helps right-size resource requests over time to reduce overprovisioning and improve utilization. These mechanisms can be beneficial for applications with unpredictable traffic patterns, batch processing jobs, or workloads that experience periodic spikes.

However, automated scaling may not be ideal in environments using ClusterResourceQuotas. Since quotas impose hard limits on resource requests across multiple namespaces, autoscalers can cause unintended issues:

- **Quota Exhaustion** – A pod scaled by VPA might increase its request so much that no other pods can start within the tenant due to reaching the quota limit.
- **Unpredictable Scaling Conflicts** – HPA and VPA can cause resources to scale unpredictably, making it difficult to plan and allocate quotas efficiently.
- **Service Disruptions** – VPA requires pod restarts when adjusting resource requests, which can cause unexpected downtime for stateful applications.
- **Resource Starvation** – Autoscalers may allocate resources unevenly, leading to some workloads consuming more than intended while others struggle to get scheduled.

Because of these risks, we do not necessarily recommend using HPA or VPA in our environment, particularly where ClusterResourceQuotas are in place. Instead, resource requests and limits should be manually defined and regularly reviewed to ensure a predictable and balanced use of resources across tenants.

## Resource Monitoring and Optimization

Defining resource requests and limits is not a one-time task. As workloads evolve, it is essential to continuously monitor resource consumption to ensure that allocations remain appropriate and do not lead to inefficiencies or instability. Monitoring tools should be used to track usage patterns, detect anomalies, and identify trends that could indicate the need for adjustments. If resource consumption deviates from expected behavior, requests and limits should be fine-tuned accordingly to prevent overprovisioning, resource starvation, or quota exhaustion. This is especially important in environments with ClusterResourceQuotas, where improper scaling can impact multiple workloads. By maintaining a proactive approach to resource management, OpenShift environments can remain stable, predictable, and efficient while meeting the needs of applications.