# Phase 0 — Topic 8: Container Registries

**Level:** Beginner to intermediate · **Format:** Hands-on · **Assignment:** 0.8

## Learning objectives

By the end of this topic, you should be able to:
- Explain what a container registry does.
- Understand repositories, image tags, and digests in a registry.
- Authenticate to a registry securely.
- Push and pull images.
- Work with Docker Hub and Amazon Elastic Container Registry (ECR).
- Understand private repositories, IAM permissions, and image lifecycle.
- Explain how Kubernetes and EKS retrieve images.

## 1. What is a container registry?

A container registry is a service for storing, managing, and distributing container images.

A typical workflow is:

```text
Source code
    |
    v
Build image
    |
    v
Tag image
    |
    v
Authenticate to registry
    |
    v
Push image
    |
    v
Registry repository
    |
    v
Pull image onto runtime / Kubernetes node
```

Examples include Docker Hub, Amazon ECR, GitHub Container Registry, and GitLab Container Registry.

## 2. Registry terminology

- **Registry:** The service endpoint that stores images.
- **Repository:** A named collection of image versions.
- **Tag:** A human-readable label for an image, such as `1.0.0`.
- **Digest:** A content-addressed identifier for an image manifest.
- **Authentication:** Establishes who or what is connecting.
- **Authorization:** Determines which actions the identity may perform.

An image reference commonly looks like:

```text
REGISTRY/REPOSITORY:TAG
123456789012.dkr.ecr.ap-south-1.amazonaws.com/my-app:1.0.0
```

## 3. Docker Hub: push and pull

You need a Docker Hub account and permission to publish to the target namespace.

Sign in using the Docker CLI:

```bash
docker login
```

Use an access token or supported credential helper rather than putting a password or token directly into shell history.

Assuming your local image is `image-lab:1.0.0`, tag it for your Docker Hub namespace:

```bash
docker tag image-lab:1.0.0 YOUR_DOCKERHUB_USERNAME/image-lab:1.0.0
```

Push:

```bash
docker push YOUR_DOCKERHUB_USERNAME/image-lab:1.0.0
```

Pull:

```bash
docker pull YOUR_DOCKERHUB_USERNAME/image-lab:1.0.0
```

Replace the placeholder with your own namespace. You need the appropriate repository permissions. Log out when finished if you are using a shared machine:

```bash
docker logout
```

## 4. Amazon ECR overview

Amazon Elastic Container Registry (ECR) is AWS's managed container image registry.

Key concepts:
- A private ECR repository stores images within an AWS account and Region.
- IAM controls which principals can authenticate and perform repository actions.
- The ECR authorization token used by Docker login is temporary.
- Repository policies can grant access to other AWS accounts or principals, subject to IAM and ECR authorization rules.
- Lifecycle policies can expire or archive images according to configured rules.
- Image scanning can help identify known vulnerabilities.

An ECR image URI usually follows this pattern:

```text
ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/REPOSITORY:TAG
```

## 5. Hands-on lab: create an ECR repository and push an image

Prerequisites:
- AWS CLI installed and configured.
- An AWS identity authorized to create/use ECR repositories and push images.
- Docker installed and running.
- A local image, for example `image-lab:1.0.0` from Topic 7.

Choose your AWS Region and account:

```bash
export AWS_REGION=ap-south-1
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
export ECR_REPOSITORY=image-lab
export IMAGE_TAG=1.0.0
export ECR_REGISTRY=${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
```

Confirm the identity and Region before making changes:

```bash
aws sts get-caller-identity
aws configure get region
echo "$AWS_REGION"
```

Create the repository (if it does not already exist):

```bash
aws ecr create-repository \
  --repository-name "$ECR_REPOSITORY" \
  --region "$AWS_REGION"
```

If it already exists, inspect it instead:

```bash
aws ecr describe-repositories \
  --repository-names "$ECR_REPOSITORY" \
  --region "$AWS_REGION"
```

Authenticate Docker to ECR:

```bash
aws ecr get-login-password --region "$AWS_REGION" \
  | docker login --username AWS --password-stdin "$ECR_REGISTRY"
```

Tag the local image with its ECR destination:

```bash
docker tag image-lab:$IMAGE_TAG \
  "$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
```

Push:

```bash
docker push "$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
```

Verify the image in ECR:

```bash
aws ecr describe-images \
  --repository-name "$ECR_REPOSITORY" \
  --region "$AWS_REGION"
```

Pull the image back:

```bash
docker pull "$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
docker image ls
```

Clean up local Docker authentication when appropriate:

```bash
docker logout "$ECR_REGISTRY"
```

**Cost and safety:** ECR storage and data transfer may incur charges. Use a learning account, confirm the account and Region before creating resources, and remove resources you no longer need. Do not delete a shared or production repository.

## 6. IAM permissions and secure access

For a push workflow, the identity generally needs permission to obtain an ECR authorization token and upload image layers/manifests to the target repository. Pull workflows need token access and permission to download image layers and manifests.

Common ECR actions include:

- `ecr:GetAuthorizationToken` — authenticate to ECR; this action uses `Resource: "*"`.
- `ecr:BatchCheckLayerAvailability`
- `ecr:InitiateLayerUpload`
- `ecr:UploadLayerPart`
- `ecr:CompleteLayerUpload`
- `ecr:PutImage`
- `ecr:BatchGetImage`
- `ecr:GetDownloadUrlForLayer`

Scope repository-level permissions to the specific repository ARN wherever the action supports resource-level permissions. Follow your organization's approved IAM policy and role-assumption model. Do not use long-lived AWS access keys in Dockerfiles or image layers.

For EKS, the node or workload image-pull identity and the registry policy must allow access to the private image. The exact configuration depends on the node type, EKS identity setup, and registry/account arrangement.

## 7. How Kubernetes uses registry images

A Pod specification references an image, for example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: image-lab
spec:
  containers:
    - name: app
      image: 123456789012.dkr.ecr.ap-south-1.amazonaws.com/image-lab:1.0.0
      ports:
        - containerPort: 5000
```

Kubernetes asks the node's container runtime to retrieve the image if it is not already available locally and policy requires a pull.

Common `imagePullPolicy` values:
- `IfNotPresent`: use a locally cached image if present; otherwise pull it.
- `Always`: resolve the image reference with the registry when starting the container. If the reference resolves to cached content with the same digest, the runtime may reuse the local content.
- `Never`: do not attempt to pull; the image must already be available locally.

For production, use immutable version references or digests and a deliberate image-pull policy. Do not rely on mutable tags for controlled releases.

## 8. Troubleshooting registry issues

| Symptom | Things to check |
|---|---|
| `no basic auth credentials` | Registry login, token expiry, credential helper |
| `denied: requested access` | Repository permissions, IAM, repository policy, namespace |
| `repository does not exist` | Registry hostname, account, Region, repository name |
| `image not found` | Tag or digest, push result, repository path |
| Kubernetes `ImagePullBackOff` | Pod events, image reference, node network access, registry auth and permissions |
| Works locally but not on EKS | Architecture compatibility, registry access, IAM, image URI, DNS/network egress |

Useful commands:

```bash
docker image ls
docker image inspect IMAGE
docker pull REGISTRY/REPOSITORY:TAG
aws ecr describe-repositories --region "$AWS_REGION"
aws ecr describe-images --repository-name "$ECR_REPOSITORY" --region "$AWS_REGION"
kubectl describe pod POD_NAME
kubectl get events --sort-by=.metadata.creationTimestamp
```

Do not paste registry passwords, authorization tokens, or AWS credentials into tickets, chat, or logs.

## Assignment 0.8 checklist

- [ ] Explain registry, repository, tag, and digest.
- [ ] Explain the difference between Docker Hub and Amazon ECR.
- [ ] Sign in to Docker Hub using a secure authentication method.
- [ ] Tag and push a lab image to a repository you control, if available.
- [ ] Pull the image back and verify it locally.
- [ ] Identify your AWS account and Region before creating ECR resources.
- [ ] Create or inspect an ECR repository in a learning account, if available.
- [ ] Authenticate Docker to ECR using the AWS CLI login-password flow.
- [ ] Tag and push a local image to ECR.
- [ ] Verify the image with `aws ecr describe-images`.
- [ ] Pull the image from ECR.
- [ ] Explain the ECR permissions needed for push vs. pull.
- [ ] Explain how an EKS node retrieves a private ECR image.
- [ ] Explain how you would troubleshoot `ImagePullBackOff`.
- [ ] Clean up local authentication and any lab resources that are no longer needed, without deleting shared resources.

## Knowledge check

1. What is the difference between a registry and a repository?
2. What does an ECR image URI contain?
3. Why does Docker need to authenticate before pushing to a private ECR repository?
4. What is the purpose of `ecr:GetAuthorizationToken`?
5. What is one cause of Kubernetes `ImagePullBackOff`?
6. Why should production deployments avoid relying on a mutable `latest` tag?

**Completion gate:** Push and pull a versioned image from a registry you control, explain the permissions involved, and describe how EKS obtains a private image. After this topic, Phase 0 foundations are complete; the next phase is [Phase 1 — Kubernetes Fundamentals](../../ROADMAP.md#phase-1--kubernetes-fundamentals).
