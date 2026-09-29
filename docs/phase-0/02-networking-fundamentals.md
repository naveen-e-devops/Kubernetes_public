# Phase 0 — Topic 2: Networking Fundamentals

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.2

## Learning objectives

By the end of this topic, you should be able to:
- Explain IPv4, public/private IP addresses, and CIDR.
- Understand basic subnetting and routing.
- Distinguish TCP from UDP and recognize common ports.
- Explain DNS resolution at a high level.
- Use command-line tools to inspect and troubleshoot connectivity.
- Run a local HTTP server and access it over TCP.

## Why networking matters in Kubernetes

Kubernetes components communicate across multiple network layers. Users reach applications through load balancers and Services; Pods communicate with other Pods and external systems; nodes communicate with the control plane. Understanding IP addresses, ports, DNS, and routing is essential for diagnosing these paths.

## 1. IP addresses

An IP address identifies a network interface.

IPv4 example: `192.168.1.10`

IPv6 example: `2001:db8::1`

Common private IPv4 ranges:

| Range | Common use |
|---|---|
| `10.0.0.0/8` | VPCs and enterprise networks |
| `172.16.0.0/12` | Private networks |
| `192.168.0.0/16` | Home and office networks |

Private addresses are intended for internal networks and are not directly routable over the public internet.

### Public vs. private IP

- **Public IP:** Routable over the internet, subject to routing and security controls.
- **Private IP:** Used within private networks; external access generally requires an appropriate gateway, proxy, or load balancer.

EKS worker nodes commonly use private IPs inside a VPC.

## 2. CIDR and subnetting

CIDR (Classless Inter-Domain Routing) represents a network prefix. For example, `10.0.0.0/16` means the first 16 bits identify the network.

| CIDR | Total IPv4 addresses |
|---|---:|
| `/16` | 65,536 |
| `/20` | 4,096 |
| `/24` | 256 |
| `/25` | 128 |
| `/26` | 64 |
| `/28` | 16 |

AWS reserves five IPv4 addresses in each subnet, so usable addresses are fewer than the total shown.

A VPC such as `10.0.0.0/16` can be divided into smaller subnets, for example:

- Public subnet A: `10.0.1.0/24`
- Public subnet B: `10.0.2.0/24`
- Private subnet A: `10.0.11.0/24`
- Private subnet B: `10.0.12.0/24`

This is an illustrative layout. Actual subnet design depends on availability zones, routing, address capacity, and workload requirements.

## 3. Ports and protocols

An IP identifies an interface; a port identifies a service endpoint on that interface. For example, `10.0.1.25:8080` refers to port 8080 on that address.

| Port | Common service |
|---|---|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6443 | Kubernetes API server |
| 8080 | Common application port |
| 9090 | Prometheus |
| 3000 | Grafana |

These are common defaults; services can be configured differently.

### TCP vs. UDP

- **TCP:** Connection-oriented, reliable, and ordered delivery. Commonly used by HTTP(S), SSH, and databases.
- **UDP:** Connectionless with lower overhead and no built-in delivery guarantee. Used by DNS queries and many real-time applications.

## 4. DNS

The Domain Name System resolves names such as `example.com` into records including IP addresses. Kubernetes also uses DNS for service discovery; a Service may be addressed by a name such as `backend.default.svc.cluster.local`. Kubernetes DNS will be explored in a later phase.

## 5. Networking commands

| Command | Purpose |
|---|---|
| `ip addr` | Show interfaces and IP addresses |
| `ip route` | Show the routing table |
| `ping google.com` | Test reachability when ICMP is permitted |
| `curl -I https://google.com` | Inspect HTTP response headers |
| `curl -v https://google.com` | Show connection and TLS details |
| `nslookup google.com` | Query DNS |
| `dig google.com` | Detailed DNS lookup |
| `ss -tuln` | List listening TCP/UDP sockets |
| `ss -tulnp` | Include process details where permitted |
| `traceroute google.com` | Trace network path, if installed and permitted |
| `nc -zv localhost 8080` | Test a TCP port |

On macOS, use `ifconfig` for interfaces, `netstat -rn` for routes, and `lsof -i -P -n` to inspect sockets. `curl`, `ping`, `dig`, and `nslookup` are also available on standard macOS installations.

Run the following and note the results:

```bash
ip addr
ip route
nslookup google.com
curl -I https://google.com
ss -tuln
ping -c 4 google.com
```

If a command is unavailable, record that rather than getting stuck installing tools.

## 6. Practical lab: local HTTP server

### Start a server

```bash
mkdir -p ~/kubernetes-learning/networking-lab
cd ~/kubernetes-learning/networking-lab
echo "Hello from my networking lab" > index.html
python3 -m http.server 8000
```

Keep the server running. In a second terminal, request the page:

```bash
curl http://localhost:8000
```

Expected response:

```text
Hello from my networking lab
```

Inspect the listening port:

Linux:
```bash
ss -tuln | grep 8000
```

macOS:
```bash
lsof -i :8000
```

Stop the server with **Ctrl+C** in the first terminal.

**What this demonstrates:** A process listens on a TCP port, and a client sends an HTTP request to that endpoint. This is a foundational concept behind applications running in containers.

## Assignment 0.2 checklist

- [ ] Explain public vs. private IP addresses.
- [ ] Explain CIDR using `10.0.0.0/16` and `10.0.1.0/24`.
- [ ] Identify your machine's IP address and default route.
- [ ] Explain TCP vs. UDP.
- [ ] Identify the purpose of ports 22, 53, 80, 443, and 6443.
- [ ] Resolve a domain using `nslookup` or `dig`.
- [ ] List listening ports using `ss` or `lsof`.
- [ ] Start a Python HTTP server on port 8000 and access it with `curl`.
- [ ] Explain what happens when you access a website by domain name.

## Knowledge check

1. What does the `/24` prefix indicate?
2. Which protocol provides reliable, ordered delivery?
3. Which port is commonly used by the Kubernetes API server?
4. What is the primary purpose of DNS?
5. What happens when you run `curl http://localhost:8000` while the lab server is running?

**Completion gate:** Complete the checklist and explain the request path from a domain name to an application endpoint. Then continue to [Topic 3 — YAML and JSON Fundamentals](03-yaml-json-fundamentals.md).
