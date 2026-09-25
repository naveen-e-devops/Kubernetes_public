# Production EKS Platform Architecture

## Complete Flow

```text
                              +----------------------+
                              |      Developer       |
                              +----------+-----------+
                                         |
                                         v
                              +----------------------+
                              |    GitHub / GitLab   |
                              |                      |
                              | Application Source   |
                              | GitOps Manifests     |
                              +----+------------+----+
                                   |            |
                              CI/CD|            |GitOps
                                   |            |
                                   v            v
                              +---------+   +---------+
                              |   ECR   |   | Argo CD |
                              +----+----+   +----+----+
                                   |              |
                                   |              v
                                   |       +-------------+
                                   +------>| Kubernetes  |
                                           | API Server  |
                                           +------+------+
                                                  |
                                                  v
                                           +-------------+
                                           |  Scheduler  |
                                           +------+------+
                                                  |
                                      Pod cannot schedule
                                                  |
                                                  v
                                           +-------------+
                                           |  Karpenter  |
                                           +------+------+
                                                  |
                                                  v
                                           +-------------+
                                           | EC2 Nodes   |
                                           +------+------+
                                                  |
                                                  v
                                           +-------------+
                                           | Application |
                                           |    Pods     |
                                           +------+------+
                                                  |
                         +------------------------+-------------------+
                         |                        |                   |
                         v                        v                   v
                     Metrics                   Logs              Traces
                         |
                         v
                    Prometheus
                         |
                         v
                      Grafana
                         |
                         v
                  Alerts / Analysis
```

## AWS Network

```text
AWS Account
|
+-- VPC
    |
    +-- Availability Zone A
    |   +-- Public Subnet
    |   +-- Private Subnet
    |
    +-- Availability Zone B
    |   +-- Public Subnet
    |   +-- Private Subnet
    |
    +-- Availability Zone C
        +-- Public Subnet
        +-- Private Subnet

Private Subnets
|
+-- EKS System Node Group
|   +-- CoreDNS
|   +-- Argo CD
|   +-- Karpenter Controller
|   +-- EKS Add-ons
|
+-- Karpenter-managed nodes
    +-- Application workloads
    +-- General workloads
    +-- Compute workloads
    +-- Spot workloads
```

## Design Principles

1. Terraform manages AWS infrastructure.
2. Argo CD manages Kubernetes application state.
3. Karpenter manages dynamic application capacity.
4. System components have stable capacity.
5. Workloads are distributed across Availability Zones.
6. Applications define resource requests and health probes.
7. Observability is built into the platform.
8. IAM and Kubernetes RBAC follow least privilege.
9. Production changes are automated and reviewable.
10. Failure scenarios are intentionally tested.

## Repository Structure

```text
kubernetes-production-learning/
|
+-- terraform/
|   +-- modules/
|   |   +-- vpc/
|   |   +-- eks/
|   |   +-- iam/
|   |   +-- karpenter/
|   |   +-- addons/
|   |   +-- observability/
|   |
|   +-- environments/
|       +-- dev/
|       +-- stage/
|       +-- prod/
|
+-- kubernetes/
+-- argocd/
+-- karpenter/
+-- monitoring/
+-- applications/
+-- docs/
```
