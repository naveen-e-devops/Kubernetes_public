# Production-Grade Kubernetes & EKS Learning Roadmap

A hands-on, step-by-step learning path from Kubernetes fundamentals to a production-oriented AWS EKS platform using Terraform, Argo CD, Karpenter, Prometheus, and Grafana.

## Target Architecture

```text
Developer
   |
   v
GitHub / GitLab
   |
   +---- CI/CD ----> Container Image ----> AWS ECR
   |
   +---- GitOps --------------------------+
                                          |
                                          v
                                      Argo CD
                                          |
                                          v
                                   Kubernetes API
                                          |
                                          v
                                      Scheduler
                                          |
                              insufficient capacity
                                          |
                                          v
                                      Karpenter
                                          |
                                          v
                                  EC2 Worker Nodes
                                          |
                                          v
                                      Applications

EKS Observability:
Applications / Nodes / Kubernetes
            |
            v
       Prometheus
            |
            v
         Grafana
            |
            v
     Alerts / Dashboards
```

## Learning Method

For every topic:

1. Learn the concept.
2. Build it manually.
3. Break it intentionally.
4. Troubleshoot it.
5. Automate it.
6. Document what you learned.
7. Complete the assignment before moving on.

---

## Phase 0 — Foundations

- [ ] Linux fundamentals
- [ ] Networking fundamentals
- [ ] DNS / HTTP / HTTPS
- [ ] Git fundamentals
- [ ] YAML / JSON
- [ ] Docker fundamentals
- [ ] Container images
- [ ] Container registries

### Assignment 0

- [ ] Build a small Python application.
- [ ] Create a Dockerfile.
- [ ] Build the image.
- [ ] Run the container locally.
- [ ] Publish the image to ECR.
- [ ] Document image vs container vs registry.

---

## Phase 1 — Kubernetes Fundamentals

- [ ] Kubernetes architecture
- [ ] Control plane
- [ ] Worker nodes
- [ ] API server
- [ ] Scheduler
- [ ] Controllers
- [ ] etcd
- [ ] kubelet
- [ ] Pods
- [ ] Namespaces
- [ ] Deployments
- [ ] ReplicaSets
- [ ] Services
- [ ] ConfigMaps
- [ ] Secrets
- [ ] kubectl
- [ ] Kubernetes events
- [ ] Basic troubleshooting

### Assignment 1

- [ ] Create a local Kubernetes cluster using kind.
- [ ] Deploy nginx.
- [ ] Scale 3 -> 5 replicas.
- [ ] Delete a Pod and observe recovery.
- [ ] Inspect logs and events.
- [ ] Explain why the Pod was recreated.

---

## Phase 2 — Kubernetes Workloads

- [ ] Deployment strategies
- [ ] Rolling updates
- [ ] Rollbacks
- [ ] StatefulSets
- [ ] DaemonSets
- [ ] Jobs
- [ ] CronJobs
- [ ] ConfigMaps
- [ ] Secrets
- [ ] Volumes
- [ ] PersistentVolumes
- [ ] PersistentVolumeClaims
- [ ] StorageClasses
- [ ] Liveness probes
- [ ] Readiness probes
- [ ] Startup probes
- [ ] CPU and memory requests
- [ ] CPU and memory limits

### Assignment 2

Deploy a Python application with:

- [ ] 3 replicas
- [ ] Resource requests
- [ ] Resource limits
- [ ] Readiness probe
- [ ] Liveness probe
- [ ] Startup probe
- [ ] ConfigMap
- [ ] Secret

Intentionally break the health endpoint and troubleshoot it.

---

## Phase 3 — Kubernetes Networking

- [ ] Pod networking
- [ ] ClusterIP
- [ ] NodePort
- [ ] LoadBalancer
- [ ] Kubernetes DNS
- [ ] CoreDNS
- [ ] Ingress
- [ ] Gateway API
- [ ] NetworkPolicy
- [ ] Service discovery

### Assignment 3

Build:

```text
Frontend -> Backend -> Database
```

Implement NetworkPolicy so:

- [ ] Frontend can reach Backend.
- [ ] Backend can reach Database.
- [ ] Frontend cannot reach Database.

---

## Phase 4 — AWS & EKS Foundations

- [ ] AWS VPC
- [ ] CIDR
- [ ] Public subnets
- [ ] Private subnets
- [ ] Route tables
- [ ] Internet Gateway
- [ ] NAT Gateway
- [ ] Security Groups
- [ ] IAM
- [ ] ECR
- [ ] EKS architecture
- [ ] Managed Node Groups
- [ ] EKS add-ons
- [ ] AWS VPC CNI
- [ ] CoreDNS
- [ ] kube-proxy
- [ ] EBS CSI
- [ ] EKS Pod Identity

### Assignment 4

Understand and document:

```text
VPC
 |
 +-- Public Subnets
 |
 +-- Private Subnets
       |
       +-- EKS Nodes
       +-- Kubernetes Workloads
```

Create an initial EKS cluster and verify it with kubectl.

---

## Phase 5 — Terraform EKS

Build the EKS platform using Terraform.

### Terraform structure

```text
terraform/
├── modules/
│   ├── vpc/
│   ├── eks/
│   ├── iam/
│   ├── karpenter/
│   ├── addons/
│   └── observability/
└── environments/
    ├── dev/
    ├── stage/
    └── prod/
```

- [ ] Terraform backend
- [ ] VPC module
- [ ] EKS module
- [ ] IAM
- [ ] Managed Node Group
- [ ] EKS add-ons
- [ ] ECR
- [ ] KMS
- [ ] Route53
- [ ] Outputs
- [ ] Variables
- [ ] Environment separation
- [ ] Terraform validation
- [ ] Terraform formatting
- [ ] Terraform plan in CI
- [ ] Terraform apply workflow

### Assignment 5

Terraform must provision:

- [ ] VPC across 3 AZs
- [ ] Public and private subnets
- [ ] NAT
- [ ] EKS cluster
- [ ] System node group
- [ ] IAM
- [ ] EKS add-ons
- [ ] ECR

---

## Phase 6 — Production EKS

- [ ] Multi-AZ architecture
- [ ] Pod anti-affinity
- [ ] Topology spread constraints
- [ ] PodDisruptionBudgets
- [ ] Multiple replicas
- [ ] Rolling deployments
- [ ] Node draining
- [ ] Availability
- [ ] Resource quotas
- [ ] LimitRanges
- [ ] Cluster capacity planning

### Assignment 6

Deploy an application with 6 replicas and distribute it across 3 AZs.

Test:

- [ ] Pod failure
- [ ] Node failure
- [ ] Node drain
- [ ] Deployment rollout
- [ ] Deployment rollback

---

## Phase 7 — Argo CD / GitOps

- [ ] Argo CD architecture
- [ ] Argo CD installation
- [ ] Applications
- [ ] Projects
- [ ] Repositories
- [ ] Automated sync
- [ ] Self-healing
- [ ] Pruning
- [ ] App of Apps
- [ ] ApplicationSet
- [ ] Environment promotion
- [ ] GitOps repository structure

### Assignment 7

Implement:

```text
Git
 |
 v
Argo CD
 |
 v
EKS
```

Change an application's replica count in Git and deploy it without running kubectl apply.

---

## Phase 8 — Karpenter

Learn Kubernetes scheduling before implementing Karpenter.

- [ ] Kubernetes scheduler
- [ ] Node selectors
- [ ] Node affinity
- [ ] Pod affinity
- [ ] Pod anti-affinity
- [ ] Taints
- [ ] Tolerations
- [ ] Topology constraints
- [ ] Karpenter architecture
- [ ] NodePool
- [ ] EC2NodeClass
- [ ] Instance types
- [ ] On-Demand capacity
- [ ] Spot capacity
- [ ] Disruption
- [ ] Consolidation
- [ ] Capacity optimization

### Assignment 8

Create:

- [ ] General NodePool
- [ ] Compute NodePool
- [ ] Spot NodePool
- [ ] Workload scheduling rules

Scale an application from 3 -> 20 replicas and observe Karpenter provision nodes.

Scale back to 3 and observe consolidation.

---

## Phase 9 — Prometheus & Grafana

- [ ] Prometheus architecture
- [ ] Metrics
- [ ] Scraping
- [ ] ServiceMonitor
- [ ] PromQL
- [ ] Recording rules
- [ ] Alert rules
- [ ] Alertmanager
- [ ] Grafana
- [ ] Dashboards
- [ ] Kubernetes metrics
- [ ] Application metrics
- [ ] Node metrics

### Assignment 9

Create a Grafana dashboard for:

- [ ] Cluster CPU
- [ ] Cluster memory
- [ ] Node count
- [ ] Pod count
- [ ] Pod restarts
- [ ] Pending Pods
- [ ] Application request rate
- [ ] Application errors
- [ ] Application latency

Create alerts for:

- [ ] High CPU
- [ ] High memory
- [ ] CrashLoopBackOff
- [ ] Unavailable deployment
- [ ] Pending Pods

---

## Phase 10 — Kubernetes Security

- [ ] Kubernetes authentication
- [ ] RBAC
- [ ] Roles
- [ ] ClusterRoles
- [ ] RoleBindings
- [ ] ServiceAccounts
- [ ] EKS Pod Identity
- [ ] NetworkPolicy
- [ ] Pod Security Standards
- [ ] Secrets
- [ ] Encryption
- [ ] Image scanning
- [ ] Admission control
- [ ] Least privilege

### Assignment 10

Create:

- [ ] readonly identity
- [ ] developer identity
- [ ] platform-admin identity

Verify that each identity can only perform its intended operations.

---

## Phase 11 — Production Operations

- [ ] EKS upgrades
- [ ] Kubernetes upgrades
- [ ] Add-on upgrades
- [ ] Karpenter upgrades
- [ ] Node AMI upgrades
- [ ] Application rollback
- [ ] Incident troubleshooting
- [ ] Capacity troubleshooting
- [ ] Backup
- [ ] Disaster recovery
- [ ] Velero
- [ ] RTO / RPO
- [ ] Cost optimization
- [ ] SLO / SLI
- [ ] Production runbooks

### Failure Scenarios

- [ ] Pod crash
- [ ] CrashLoopBackOff
- [ ] Bad image
- [ ] Bad configuration
- [ ] Node failure
- [ ] Node drain
- [ ] Insufficient capacity
- [ ] Karpenter failure
- [ ] Argo CD failure
- [ ] Prometheus failure
- [ ] AZ capacity issue

---

## Phase 12 — Production EKS Capstone

Build the complete platform.

### Infrastructure

```text
AWS
├── VPC
│   ├── 3 AZs
│   ├── Public Subnets
│   └── Private Subnets
├── EKS
│   ├── Control Plane
│   ├── System Node Group
│   └── Add-ons
├── ECR
├── IAM
├── KMS
├── Route53
└── Load Balancer
```

### Platform

```text
EKS
├── kube-system
├── argocd
├── karpenter
├── monitoring
├── ingress
└── applications
```

### Final requirements

- [ ] Terraform provisions infrastructure.
- [ ] Argo CD manages Kubernetes workloads.
- [ ] Karpenter manages application capacity.
- [ ] Prometheus collects metrics.
- [ ] Grafana provides dashboards.
- [ ] Kubernetes RBAC is implemented.
- [ ] NetworkPolicies are implemented.
- [ ] Workloads are distributed across AZs.
- [ ] Applications have probes and resource requests.
- [ ] CI validates Terraform and Kubernetes changes.
- [ ] Upgrade procedure is documented.
- [ ] Disaster recovery procedure is documented.
- [ ] Troubleshooting runbook is documented.

---

## Final Architecture

See [docs/architecture.md](docs/architecture.md).

## Progress

| Phase | Topic | Status |
|---|---|---|
| 0 | Foundations | ⬜ Not Started |
| 1 | Kubernetes Fundamentals | ⬜ Not Started |
| 2 | Kubernetes Workloads | ⬜ Not Started |
| 3 | Kubernetes Networking | ⬜ Not Started |
| 4 | AWS & EKS | ⬜ Not Started |
| 5 | Terraform EKS | ⬜ Not Started |
| 6 | Production EKS | ⬜ Not Started |
| 7 | Argo CD / GitOps | ⬜ Not Started |
| 8 | Karpenter | ⬜ Not Started |
| 9 | Prometheus & Grafana | ⬜ Not Started |
| 10 | Security | ⬜ Not Started |
| 11 | Production Operations | ⬜ Not Started |
| 12 | Capstone | ⬜ Not Started |
