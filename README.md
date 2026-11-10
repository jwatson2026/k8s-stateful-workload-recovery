\# Kubernetes Stateful Workload Recovery



\## Overview

Validated Kubernetes workload recovery and persistent data availability using Docker Desktop Kubernetes, a Deployment, and dynamically provisioned persistent storage.



\## Scenario

A containerized workload must retain its stored data when a Pod is terminated and replaced. This scenario evaluates Kubernetes reconciliation and persistent volume reuse during controlled Pod failure.



\## Environment

\- Platform: Docker Desktop Kubernetes

\- Kubernetes version: v1.36.1

\- Node: desktop-control-plane

\- Container image: busybox:1.36

\- Deployment: stateful-workload

\- StorageClass: standard (rancher.io/local-path)

\- PersistentVolumeClaim: workload-data

\- Requested capacity: 1Gi

\- Access mode: ReadWriteOnce



\## Architecture

Deployment (1 replica) -> Pod -> /data volume mount -> PVC (workload-data) -> PV (1Gi)



\## Implementation

1\. Created a PersistentVolumeClaim using pvc.yaml.

2\. Deployed a BusyBox workload using deployment.yaml.

3\. Verified the Pod was Running and the PVC/PV were Bound.

4\. Wrote a recovery test file to /data/recovery-test.txt.

5\. Confirmed the file contents before deleting the Pod.

6\. Deleted the original Pod to simulate workload failure.

7\. Verified Kubernetes created a replacement Pod.

8\. Confirmed the replacement Pod could read the original file.

9\. Checked the Deployment, Pod, PVC, and PV after recovery.



\## Recovery Validation

| Validation | Result |

| --- | --- |

| Deployment availability | 1/1 |

| Original Pod | stateful-workload-5c6f975c97-kcngt |

| Replacement Pod | stateful-workload-5c6f975c97-2jlgq |

| Replacement Pod status | Running, 1/1 Ready |

| PVC | Bound |

| PV | Bound, 1Gi |

| Data after replacement | Kubernetes-storage-recovery-verified |



\## Key Findings

\- The Deployment restored its desired replica count after Pod deletion.

\- The replacement Pod received a new identity and successfully started.

\- The existing PVC remained Bound to its PV.

\- The replacement Pod accessed the original file through the persistent volume mount.



\## Repository Files

\- deployment.yaml — Kubernetes Deployment and volume mount configuration

\- pvc.yaml — PersistentVolumeClaim definition

\- infrastructure-validation.txt — Post-recovery resource status evidence



\## Scope and Limitations

This is a controlled single-node Docker Desktop recovery test, not a production high-availability or disaster-recovery implementation. The storage uses a local-path provisioner and does not demonstrate resilience to node loss or underlying disk failure. A single replica may experience downtime during replacement.



