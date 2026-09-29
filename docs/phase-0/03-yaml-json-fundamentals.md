# Phase 0 — Topic 3: YAML and JSON Fundamentals

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.3

## Learning objectives

By the end of this topic, you should be able to:
- Read and write YAML mappings, sequences, and nested structures.
- Recognize strings, integers, floats, booleans, and null values.
- Understand indentation and common YAML parsing errors.
- Read equivalent YAML and JSON representations.
- Validate YAML syntax with command-line tools or Python.
- Explain why Kubernetes uses declarative manifests.

## 1. Why YAML matters

Kubernetes is declarative: you describe the desired state of resources, and Kubernetes controllers work to make the actual state match it. YAML manifests are a common way to express this configuration.

YAML is used in Kubernetes manifests, Helm values, Argo CD applications, CI/CD pipelines, and many other configuration systems. JSON is common in APIs and application data.

## 2. Basic YAML

```yaml
name: my-application
environment: development
replicas: 3
enabled: true
```

This is a mapping of keys to values. The values above are a string, string, integer, and boolean.

## 3. Indentation and nesting

```yaml
application:
  name: payment-service
  replicas: 3
  image:
    repository: nginx
    tag: "1.27"
```

The `image` mapping is nested under `application`. Its `repository` and `tag` keys are nested under `image`.

Rules:
1. Use spaces, not tabs.
2. Keep indentation consistent.
3. Two spaces per level is a common convention.
4. Child keys must be indented beneath their parent.
5. Incorrect indentation can cause parsing errors or change the structure.

## 4. Core data structures

### Mappings (key-value pairs)

```yaml
server:
  host: localhost
  port: 8080
  protocol: http
```

### Sequences (lists)

```yaml
environments:
  - development
  - staging
  - production
```

A sequence can contain objects:

```yaml
developers:
  - name: Alice
    role: DevOps Engineer
  - name: Bob
    role: Platform Engineer
```

### Nested objects

```yaml
application:
  name: order-service
  database:
    host: db.example.com
    port: 5432
    credentials:
      username: app_user
      password: change-me
```

The password above is illustrative only. Do not commit real credentials to source control; secret management will be covered later.

## 5. Data types and quoting

```yaml
application: payment-service
replicas: 3
cpu_threshold: 75.5
enabled: true
maintenance_mode: false
description: null
ports:
  - 8080
  - 8443
resources:
  memory: 512Mi
  cpu: "500m"
```

Quoted values are strings:

```yaml
version: "1.0"
build_number: "00123"
enabled: "false"
```

In the last example, `enabled` is a string, not a boolean. Quote values intentionally, especially in Helm values and environment-variable configuration.

## 6. Comments

Comments begin with `#`:

```yaml
# Application configuration
name: payment-service
replicas: 3
```

Use comments to explain decisions or context rather than merely restating obvious values.

## 7. JSON basics

```json
{
  "name": "payment-service",
  "replicas": 3,
  "enabled": true,
  "ports": [8080, 8443]
}
```

JSON uses curly braces for objects, square brackets for arrays, double quotes for strings and property names, and commas between entries. Standard JSON does not support comments.

### YAML and JSON comparison

| Feature | YAML | JSON |
|---|---|---|
| Indentation | Significant | Not significant |
| Comments | Supported | Not in standard JSON |
| Strings | Quotes often optional | Double quotes required |
| Lists | Hyphen notation | Square brackets |
| Objects | Indentation-based | Curly braces |
| Common use | Kubernetes and tool configuration | APIs and application data |

Equivalent YAML:

```yaml
application:
  name: payment-service
  replicas: 2
  ports:
    - 8080
    - 8443
```

Equivalent JSON:

```json
{
  "application": {
    "name": "payment-service",
    "replicas": 2,
    "ports": [8080, 8443]
  }
}
```

## 8. Hands-on lab: Kubernetes-style configuration

Create a workspace and file:

```bash
mkdir -p ~/kubernetes-learning/yaml-lab
cd ~/kubernetes-learning/yaml-lab
```

Create `application.yaml`:

```yaml
application:
  name: payment-service
  environment: development

  replicas: 2

  container:
    image: nginx
    tag: "1.27"
    port: 8080

  resources:
    requests:
      cpu: "100m"
      memory: "128Mi"
    limits:
      cpu: "500m"
      memory: "512Mi"

  health_check:
    enabled: true
    path: /health
    interval_seconds: 10

  environments:
    - development
    - staging
    - production
```

This is a learning configuration, not a Kubernetes Deployment manifest. Its purpose is to practice YAML syntax before learning Kubernetes-specific schemas.

Inspect it with:

```bash
cat application.yaml
```

Identify the root object, nested mappings, list, and different value types.

## 9. Validate YAML

Install yamllint if Python and pip are available:

```bash
python3 -m pip install --user yamllint
yamllint application.yaml
```

The linter may report style warnings even when syntax is valid. Learn to distinguish style guidance from parsing errors.

Alternatively, install PyYAML:

```bash
python3 -m pip install --user PyYAML
```

Create `validate_yaml.py`:

```python
import sys
import yaml

file_path = sys.argv[1]

try:
    with open(file_path, encoding="utf-8") as file:
        data = yaml.safe_load(file)

    print("YAML is valid")
    print(data)

except yaml.YAMLError as error:
    print(f"YAML validation failed: {error}")
    sys.exit(1)
```

Run it:

```bash
python3 validate_yaml.py application.yaml
```

Expected result begins with:

```text
YAML is valid
```

This checks whether the file parses as YAML. It does not validate Kubernetes resource schemas; Kubernetes-specific validation comes later.

## 10. Troubleshooting challenge

Intentionally break the indentation:

```yaml
container:
  image: nginx
    tag: "1.27"
```

Run the validator, identify the error, fix the indentation, and validate again.

## Assignment 0.3 checklist

- [ ] Explain YAML vs. JSON.
- [ ] Create a YAML file with strings, integers, booleans, lists, and nested objects.
- [ ] Create a list of three applications, each with a name, image, and port.
- [ ] Create nested application, database, and monitoring settings.
- [ ] Convert a YAML configuration into equivalent JSON.
- [ ] Install and run a YAML validator.
- [ ] Introduce an indentation error and identify it.
- [ ] Correct the file and validate it successfully.
- [ ] Explain why Kubernetes uses declarative manifests.

## Knowledge check

1. Why is indentation important in YAML?
2. What is the difference between a mapping and a sequence?
3. Is `"false"` a boolean or a string?
4. Does standard JSON support comments?
5. Does a YAML parser validate that a Kubernetes manifest is a valid Deployment?

**Completion gate:** Complete the checklist and explain the structure of your YAML file. Then continue to the next Phase 0 topic in the roadmap.
