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


# Learning Milestones

These milestones are the checkpoints we will use to decide whether you are ready to move to the next level. A phase can be technically completed while a milestone requires you to demonstrate that you can actually build, troubleshoot, explain, and automate the platform.

## 🟢 Milestone 1 — Kubernetes Developer

### Knowledge
- [ ] Explain Kubernetes architecture.
- [ ] Explain control plane and worker-node responsibilities.
- [ ] Explain Pods, Deployments, ReplicaSets, and Services.
- [ ] Explain ConfigMaps and Secrets.
- [ ] Explain resource requests and limits.
- [ ] Explain readiness, liveness, and startup probes.
- [ ] Understand basic Kubernetes networking.

### Practical skills
- [ ] Deploy an application.
- [ ] Scale an application.
- [ ] Perform a rolling update.
- [ ] Roll back a deployment.
- [ ] Troubleshoot CrashLoopBackOff.
- [ ] Troubleshoot Pending Pods.
- [ ] Troubleshoot a Service that cannot reach Pods.

### Milestone project
Build a production-like local application with:
- [ ] 3 replicas
- [ ] Health probes
- [ ] Resource requests and limits
- [ ] ConfigMap
- [ ] Secret
- [ ] Service
- [ ] NetworkPolicy

**Gate:** Explain the complete request path and troubleshoot a deliberately broken deployment without following a copy/paste solution.

---

## 🟡 Milestone 2 — Kubernetes Administrator

### Knowledge
- [ ] Scheduling basics.
- [ ] Nodes and node lifecycle.
- [ ] Storage.
- [ ] Kubernetes DNS.
- [ ] NetworkPolicy.
- [ ] RBAC.
- [ ] PodDisruptionBudget.
- [ ] Affinity and anti-affinity.
- [ ] Topology spread constraints.
- [ ] Kubernetes troubleshooting workflow.

### Practical skills
- [ ] Drain a node safely.
- [ ] Recover from node failure.
- [ ] Debug DNS and networking.
- [ ] Debug storage issues.
- [ ] Implement RBAC.
- [ ] Distribute workloads for high availability.

### Milestone project
Build a multi-node environment and demonstrate:
- [ ] Workload distribution.
- [ ] Node failure recovery.
- [ ] Network isolation.
- [ ] RBAC restrictions.
- [ ] Safe node draining.

**Gate:** Given a broken workload, identify whether the failure is caused by the application, Pod, Service, network, node, or scheduler.

---

## 🟠 Milestone 3 — AWS EKS Engineer

### Knowledge
- [ ] AWS VPC architecture.
- [ ] Public vs private subnets.
- [ ] Route tables.
- [ ] NAT Gateway.
- [ ] Security Groups.
- [ ] IAM.
- [ ] ECR.
- [ ] EKS architecture.
- [ ] EKS add-ons.
- [ ] AWS VPC CNI.
- [ ] EBS CSI.
- [ ] EKS Pod Identity.

### Practical skills
- [ ] Design an EKS VPC.
- [ ] Create an EKS cluster.
- [ ] Create managed node groups.
- [ ] Configure EKS add-ons.
- [ ] Deploy an application.
- [ ] Expose an application.
- [ ] Troubleshoot AWS/Kubernetes networking.

### Milestone project
Build:
```text
AWS VPC
   |
   v
EKS
   |
   v
Managed Node Group
   |
   v
Kubernetes Application
```

**Gate:** Explain how traffic, IAM, networking, DNS, and compute interact from AWS infrastructure to a running Pod.

---

## 🔵 Milestone 4 — Terraform EKS Engineer

### Knowledge
- [ ] Terraform modules.
- [ ] Terraform state.
- [ ] Remote backend.
- [ ] Variables and outputs.
- [ ] Resource dependencies.
- [ ] IAM with Terraform.
- [ ] EKS with Terraform.
- [ ] Environment separation.
- [ ] CI validation and plan workflows.

### Practical skills
- [ ] Build reusable VPC modules.
- [ ] Build reusable EKS modules.
- [ ] Manage IAM with Terraform.
- [ ] Manage EKS add-ons.
- [ ] Manage ECR.
- [ ] Run Terraform validation and formatting checks.
- [ ] Generate and review Terraform plans.
- [ ] Apply infrastructure through an automated workflow.

### Milestone project
Provision the complete EKS foundation using Terraform with no manual AWS console configuration:
```text
Terraform
   |
   +--> VPC
   +--> IAM
   +--> EKS
   +--> System Node Group
   +--> ECR
   +--> Add-ons
```

**Gate:** Destroy and recreate the environment from code and explain Terraform state, dependencies, and recovery considerations.

---

## 🟣 Milestone 5 — Platform Engineer

### Knowledge
- [ ] GitOps principles.
- [ ] Argo CD architecture.
- [ ] Argo CD Applications.
- [ ] ApplicationSets.
- [ ] Environment promotion.
- [ ] Kubernetes scheduling.
- [ ] Karpenter architecture.
- [ ] NodePools.
- [ ] EC2NodeClass.
- [ ] On-Demand and Spot capacity.
- [ ] Consolidation and disruption.
- [ ] Prometheus.
- [ ] PromQL.
- [ ] Grafana.
- [ ] Alerting.

### Practical skills
- [ ] Deploy applications with Argo CD.
- [ ] Implement self-healing.
- [ ] Implement Git-based promotion.
- [ ] Configure Karpenter.
- [ ] Provision nodes dynamically.
- [ ] Use Spot capacity appropriately.
- [ ] Observe scheduling decisions.
- [ ] Build Prometheus queries.
- [ ] Build Grafana dashboards.
- [ ] Create operational alerts.

### Milestone project
Build the complete platform flow:
```text
Git
 |
 v
CI/CD
 |
 v
ECR
 |
 v
Argo CD
 |
 v
EKS
 |
 +--> Scheduler
       |
       v
   Karpenter
       |
       v
     EC2
       |
       v
 Application

EKS
 |
 v
Prometheus
 |
 v
Grafana
 |
 v
Alerts
```

**Gate:** Scale workloads beyond existing capacity, explain why a node was provisioned, trace the deployment from Git to Pod, and diagnose a failed deployment using observability data.

---

## 🔴 Milestone 6 — Production Kubernetes / Platform Engineer

### Knowledge
- [ ] High availability.
- [ ] Disaster recovery.
- [ ] Backup and restore.
- [ ] EKS and Kubernetes upgrades.
- [ ] Security and least privilege.
- [ ] RBAC.
- [ ] NetworkPolicy.
- [ ] Cost optimization.
- [ ] SLO / SLI concepts.
- [ ] Incident management.
- [ ] Capacity planning.
- [ ] Production troubleshooting.

### Practical skills
- [ ] Perform an EKS upgrade.
- [ ] Perform a node upgrade.
- [ ] Recover from node failure.
- [ ] Recover from a bad deployment.
- [ ] Troubleshoot Karpenter failure.
- [ ] Troubleshoot Argo CD failure.
- [ ] Troubleshoot observability failure.
- [ ] Perform backup and restore.
- [ ] Test disaster recovery.
- [ ] Write production runbooks.
- [ ] Document architecture and operational procedures.

### Final capstone
Build and operate the complete production-oriented EKS platform defined in Phase 12.

**Final certification checklist**
- [ ] Explain the complete architecture without notes.
- [ ] Troubleshoot Pods and deployments.
- [ ] Troubleshoot Kubernetes networking and DNS.
- [ ] Design an EKS VPC.
- [ ] Provision EKS using Terraform.
- [ ] Implement GitOps with Argo CD.
- [ ] Explain Kubernetes scheduling.
- [ ] Configure Karpenter NodePools.
- [ ] Explain dynamic node provisioning.
- [ ] Write useful PromQL queries.
- [ ] Build Grafana dashboards.
- [ ] Implement RBAC.
- [ ] Implement NetworkPolicies.
- [ ] Perform upgrades.
- [ ] Recover from production failure scenarios.
- [ ] Demonstrate backup and disaster recovery.
- [ ] Explain the complete Git -> CI/CD -> ECR -> Argo CD -> EKS -> Karpenter -> Application flow.

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
