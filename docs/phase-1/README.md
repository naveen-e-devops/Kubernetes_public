# Phase 1 — Kubernetes Fundamentals

## Phase goal

Build a strong, hands-on understanding of Kubernetes before moving into AWS EKS, Terraform, Argo CD, or Karpenter.

By the end of this phase, you should be able to create and inspect a local Kubernetes cluster, deploy and expose an application, understand how Kubernetes maintains desired state, configure application settings and resource controls, and troubleshoot common workload failures.

**Recommended lab environment:** [kind](https://kind.sigs.k8s.io/) (Kubernetes in Docker). You can use Minikube if you prefer. We will use local clusters first so that learning and experimentation do not incur AWS costs.

## Learning approach

For each topic:

1. Understand the concept and the Kubernetes objects involved.
2. Read and explain the YAML manifest.
3. Apply it to the lab cluster.
4. Inspect the resulting resources and events.
5. Change or break something deliberately.
6. Troubleshoot the behaviour.
7. Record the commands, observations, and lessons learned.

Do not move to the next topic until you can explain the key concepts and complete the topic assignment. Ask for help reviewing your answers whenever needed.

## Prerequisites

- [ ] Complete the Phase 0 foundations, especially Linux, networking, YAML/JSON, Git, Docker, images, and registries.
- [ ] Install Docker Engine or Docker Desktop.
- [ ] Install kubectl.
- [ ] Install kind (or Minikube).
- [ ] Verify Docker, kubectl, and kind versions.
- [ ] Create a Git branch or working directory for Kubernetes lab manifests.

## Topic plan

| Topic | Subject | What you will learn | Practical outcome |
|---|---|---|---|
| 1 | [Kubernetes architecture and components](01-kubernetes-architecture.md) | Cluster, control plane, worker nodes, API server, etcd, scheduler, controllers, kubelet, runtime, Pods, desired state | Explain the lifecycle of a Deployment and how Kubernetes reconciles state |
| 2 | Local cluster setup with kind | Cluster prerequisites, kind configuration, control-plane and worker nodes, cluster contexts | Create and inspect a local multi-node Kubernetes cluster |
| 3 | kubectl fundamentals | Contexts, namespaces, get, describe, apply, delete, logs, exec, events, output formats | Operate and inspect the cluster from the command line |
| 4 | Pods and ReplicaSets | Pod lifecycle, containers in a Pod, labels, selectors, ReplicaSet behaviour | Create Pods and observe ReplicaSet replacement behaviour |
| 5 | Deployments and rollouts | Declarative manifests, replicas, rolling updates, rollout status, history, undo | Deploy nginx, scale it, update its image, and roll back |
| 6 | Services and service discovery | ClusterIP, NodePort, LoadBalancer concepts, selectors, endpoints, DNS basics | Reach application Pods through a stable Service |
| 7 | Namespaces and labels | Namespace isolation, labels, selectors, annotations, resource organisation | Organise workloads and query resources by labels |
| 8 | ConfigMaps and Secrets | External configuration, environment variables, mounted configuration, Secret handling and limitations | Configure an application without hard-coding settings in its image |
| 9 | Resource requests and limits | CPU and memory units, scheduling requests, limits, throttling, OOMKilled | Set and inspect workload resource requirements |
| 10 | Probes and health checks | Startup, readiness, and liveness probes; probe failure behaviour | Configure health checks and observe restart/traffic effects |
| 11 | Workload troubleshooting | Events, logs, describe, scheduling errors, image pull failures, crash loops, Services | Diagnose deliberately broken workloads using a repeatable workflow |

## Topic details and completion criteria

### Topic 1 — Kubernetes architecture and components

**Concepts**
- What Kubernetes orchestrates and why.
- Cluster, control plane, and worker nodes.
- API server, etcd, scheduler, controller manager, and cloud controller manager.
- Kubelet, container runtime, and kube-proxy (including the fact that some networking implementations replace or omit kube-proxy).
- Pods, Deployments, and ReplicaSets.
- Declarative desired state and reconciliation.

**Assignment 1.1**
- [ ] Explain why container orchestration is useful.
- [ ] Draw a Kubernetes cluster showing the control plane and worker nodes.
- [ ] Explain the responsibility of the API server, etcd, scheduler, and controller manager.
- [ ] Explain kubelet, container runtime, and kube-proxy.
- [ ] Describe what happens when a Deployment requesting three replicas is applied.
- [ ] Explain how Kubernetes responds when one of the three Pods is deleted.

**Completion evidence:** Your architecture diagram and written answers, reviewed together before proceeding.

### Topic 2 — Local cluster setup

**Lab**
- Install kind and kubectl.
- Create a cluster with one control-plane node and at least two worker nodes (where supported by the local machine).
- Inspect nodes and cluster information.
- List the current kubectl context.
- Delete and recreate the lab cluster.

**Assignment 1.2**
- [ ] Create the cluster from a kind configuration file.
- [ ] Show all nodes with `kubectl get nodes -o wide`.
- [ ] Explain the difference between a kind node and an application Pod.
- [ ] Save the cluster configuration in the repository.

### Topic 3 — kubectl fundamentals

**Commands to practise**
- `kubectl config get-contexts`
- `kubectl config current-context`
- `kubectl get`
- `kubectl describe`
- `kubectl apply -f`
- `kubectl delete -f`
- `kubectl logs`
- `kubectl exec`
- `kubectl get events`
- `kubectl explain`

**Assignment 1.3**
- [ ] Inspect nodes, namespaces, Pods, and Deployments.
- [ ] Use describe to inspect a Pod.
- [ ] Read container logs.
- [ ] Execute a command inside a running container.
- [ ] Explain the difference between get and describe.

### Topic 4 — Pods and ReplicaSets

**Assignment 1.4**
- [ ] Create a Pod from a YAML manifest.
- [ ] Inspect its status, IP, node, and events.
- [ ] Create a ReplicaSet with three replicas.
- [ ] Delete one managed Pod and observe replacement.
- [ ] Explain labels and selectors.

### Topic 5 — Deployments and rollouts

**Assignment 1.5**
- [ ] Create an nginx Deployment with three replicas.
- [ ] Scale it to five replicas.
- [ ] Update the container image.
- [ ] Watch rollout status.
- [ ] Inspect rollout history.
- [ ] Roll back to the previous revision.
- [ ] Explain the relationship between Deployment, ReplicaSet, and Pod.

### Topic 6 — Services and service discovery

**Assignment 1.6**
- [ ] Create a ClusterIP Service for the nginx Deployment.
- [ ] Inspect its selector and endpoints.
- [ ] Access the Service from another Pod.
- [ ] Use port-forwarding to access it from the local machine.
- [ ] Explain why a Service provides a stable endpoint while Pods can be replaced.

### Topic 7 — Namespaces and labels

**Assignment 1.7**
- [ ] Create a namespace for a test application.
- [ ] Deploy resources into that namespace.
- [ ] Query resources using label selectors.
- [ ] Explain the difference between labels and annotations.
- [ ] Explain what namespaces do and do not isolate.

### Topic 8 — ConfigMaps and Secrets

**Assignment 1.8**
- [ ] Create a ConfigMap from a manifest.
- [ ] Consume ConfigMap values as environment variables.
- [ ] Mount ConfigMap data into a container.
- [ ] Create a Kubernetes Secret for a lab-only value.
- [ ] Consume the Secret in a Pod.
- [ ] Explain why base64 encoding is not encryption and why Kubernetes Secrets require appropriate access controls and encryption-at-rest configuration.

Never commit real credentials, tokens, or production secrets to this learning repository.

### Topic 9 — Resource requests and limits

**Assignment 1.9**
- [ ] Set CPU and memory requests on a container.
- [ ] Set CPU and memory limits.
- [ ] Inspect resource configuration.
- [ ] Observe a scheduling or runtime effect in a controlled lab.
- [ ] Explain CPU throttling and OOMKilled at a high level.

### Topic 10 — Probes and health checks

**Assignment 1.10**
- [ ] Configure a startup probe.
- [ ] Configure a readiness probe.
- [ ] Configure a liveness probe.
- [ ] Make a health endpoint fail and observe the result.
- [ ] Explain why readiness and liveness are not interchangeable.

### Topic 11 — Workload troubleshooting

Practise diagnosing:
- [ ] Pending Pods.
- [ ] ImagePullBackOff / ErrImagePull.
- [ ] CrashLoopBackOff.
- [ ] OOMKilled.
- [ ] Failed readiness or liveness probes.
- [ ] Service with no matching endpoints.
- [ ] Incorrect configuration.
- [ ] Scheduling constraints or insufficient resources.

**Assignment 1.11**
For each scenario, capture the symptom, commands used, relevant event/log output, root cause, fix, and prevention.

## Phase 1 capstone — Deploy and troubleshoot a small application

Build a small application stack on the local cluster. You may use nginx initially, then replace it with a small application image from Phase 0.

**Required resources**
- [ ] Dedicated namespace.
- [ ] Deployment with three replicas.
- [ ] ClusterIP Service.
- [ ] ConfigMap for non-sensitive configuration.
- [ ] Lab Secret for a non-production value.
- [ ] CPU and memory requests and limits.
- [ ] Startup, readiness, and liveness probes.
- [ ] Labels on all relevant resources.

**Required demonstrations**
- [ ] Access the application through its Service.
- [ ] Scale from three to five replicas.
- [ ] Perform a rolling update.
- [ ] Roll back the Deployment.
- [ ] Delete a Pod and observe recovery.
- [ ] Cause a controlled health-check failure and explain the result.
- [ ] Diagnose one image-pull or configuration failure.
- [ ] Explain the request path from client to Service to Pod.

**Deliverables**
- [ ] Kubernetes manifests stored in Git.
- [ ] A short README with setup and cleanup commands.
- [ ] Architecture diagram.
- [ ] Troubleshooting notes with command output (redact sensitive data).
- [ ] Answers to the capstone review questions.

## Exit criteria — ready for Phase 2

- [ ] Can explain the Kubernetes architecture without relying on notes.
- [ ] Can create and inspect a local cluster.
- [ ] Can use kubectl confidently for common operations.
- [ ] Can deploy, scale, update, and roll back an application.
- [ ] Understands Pods, ReplicaSets, Deployments, and Services.
- [ ] Can configure namespaces, labels, ConfigMaps, and Secrets.
- [ ] Understands resource requests, limits, and probes.
- [ ] Can troubleshoot common workload failures using evidence from events, logs, and resource descriptions.
- [ ] Has completed and documented the Phase 1 capstone.

## What comes next

Phase 2 will build on these fundamentals and cover more workload types and production workload patterns, including StatefulSets, DaemonSets, Jobs, CronJobs, persistent storage, scheduling controls, and availability practices.

---

**Learning rule:** We will work through one topic at a time. Topic 1 is the current starting point; complete Assignment 1.1 and review it before starting Topic 2.
