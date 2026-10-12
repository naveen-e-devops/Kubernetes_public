# Phase 0 — Topic 5: DNS, HTTP and HTTPS

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.5

## Learning objectives

By the end of this topic, you should be able to:
- Explain how DNS resolves a domain name.
- Understand common DNS record types and caching.
- Describe an HTTP request and response.
- Recognize common HTTP methods and status codes.
- Explain the role of TCP, TLS, and HTTPS.
- Troubleshoot DNS, HTTP, and TLS problems with command-line tools.
- Explain how these concepts relate to Kubernetes Services, Ingress, and AWS load balancers.

## 1. The request flow

When a user visits `https://myapp.example.com`, the simplified flow is:

```text
Browser
  |
  v
DNS resolution -> IP address
  |
  v
TCP connection to the destination
  |
  v
TLS handshake and certificate validation
  |
  v
HTTP request over the encrypted connection
  |
  v
HTTP response (status, headers, body)
```

Real systems may also use caches, proxies, CDNs, connection reuse, and load balancers.

## 2. DNS — Domain Name System

DNS maps names to records that clients use to find services. A resolver may check caches and query DNS servers when an answer is not cached.

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| CNAME | Alias from one name to another name |
| MX | Mail routing |
| TXT | Text data, often used for domain verification and email policy |
| NS | Authoritative name servers |

DNS records have TTLs (time to live), which influence how long resolvers may cache an answer. DNS changes are not necessarily visible everywhere immediately because caches can retain previous answers until their TTL expires.

Inspect DNS:

```bash
nslookup example.com
dig example.com
dig example.com A
dig example.com AAAA
dig example.com CNAME
```

A query may return no record for a type that the domain does not publish. This is not automatically a DNS failure.

## 3. HTTP request and response

HTTP is an application-layer protocol used to exchange requests and responses. A request contains a method, path, headers, and sometimes a body. A response contains a status code, headers, and sometimes a body.

Example request:

```http
GET /health HTTP/1.1
Host: example.com
```

Example response:

```http
HTTP/1.1 200 OK
Content-Type: text/plain

healthy
```

| Method | Typical purpose |
|---|---|
| GET | Retrieve a resource |
| POST | Submit data or request an action |
| PUT | Replace or create a resource at a known URI |
| PATCH | Partially update a resource |
| DELETE | Request deletion of a resource |
| HEAD | Retrieve headers without the response body |

## 4. HTTP status codes

| Code / range | Meaning |
|---|---|
| 2xx | Success |
| 200 | Request succeeded |
| 301 / 302 | Redirect |
| 4xx | Client-side request issue |
| 400 | Bad request |
| 401 | Authentication required or failed |
| 403 | Forbidden |
| 404 | Resource not found |
| 5xx | Server or gateway issue |
| 500 | Internal server error |
| 502 | Gateway received an invalid response from an upstream |
| 503 | Service unavailable |
| 504 | Gateway timed out waiting for an upstream |

Status codes alone do not identify the root cause. Check application logs, load balancer logs, upstream health, DNS answers, and connectivity.

## 5. HTTPS and TLS

HTTPS is HTTP carried over TLS encryption. TLS helps provide:
- **Confidentiality:** Traffic is encrypted in transit.
- **Integrity:** Changes to traffic can be detected.
- **Authentication:** Certificates help clients verify the server's identity.

During a TLS handshake, client and server negotiate cryptographic settings and establish session keys. The client validates the certificate chain, validity dates, and whether the certificate matches the hostname, subject to its trust configuration.

Common TLS problems include:
- Expired certificate.
- Hostname mismatch.
- Missing intermediate certificate.
- Untrusted certificate authority.
- Incorrect system clock.
- Client/server TLS configuration mismatch.

Useful commands:

```bash
curl -I https://example.com
curl -v https://example.com
openssl s_client -connect example.com:443 -servername example.com
```

Do not disable certificate validation as a permanent fix. Diagnose the certificate chain, hostname, trust store, and time configuration.

## 6. Hands-on lab

### A. Inspect DNS

```bash
nslookup example.com
dig example.com
dig example.com A
```

Record the answer, TTL if displayed, and whether the result contains an IPv4 address.

### B. Inspect HTTP

```bash
curl -I http://example.com
curl -I https://example.com
curl -v https://example.com
```

Observe redirects, response codes, headers, and TLS connection details. Public websites may change their responses, so the exact output can differ.

### C. Inspect a TLS certificate

```bash
openssl s_client -connect example.com:443 -servername example.com
```

Look for certificate details and the verification result. Press Ctrl+C to exit if the connection remains open.

### D. Compare DNS and HTTP failures

Try a deliberately invalid hostname:

```bash
curl -v https://this-host-should-not-exist.invalid
```

The reserved `.invalid` domain should fail to resolve. Compare the error with an HTTP 404 from a real server: DNS failure means the client could not resolve the name; HTTP 404 means an HTTP server responded that the requested resource was not found.

## 7. Kubernetes and AWS connection

- **Amazon Route 53:** AWS DNS service used to host DNS zones and records.
- **Load balancer:** Receives client traffic and forwards it to targets.
- **Kubernetes Service:** Provides a stable endpoint for a set of Pods.
- **Ingress:** Defines HTTP/HTTPS routing into a cluster when paired with an ingress controller or implementation.
- **TLS Secret / certificate integration:** Supplies certificate material to supported ingress components; production setups may also automate certificate lifecycle.

A typical request path could be:

```text
Browser
  -> Route 53 DNS
  -> AWS load balancer
  -> Kubernetes ingress / Service
  -> Application Pod
```

The actual path depends on the chosen AWS and Kubernetes networking design.

## Assignment 0.5 checklist

- [ ] Explain what DNS does and what an A record contains.
- [ ] Explain A, AAAA, CNAME, and MX records.
- [ ] Explain DNS TTL and caching.
- [ ] Use `dig` or `nslookup` to resolve a domain.
- [ ] Explain the structure of an HTTP request and response.
- [ ] Recognize GET, POST, PUT, PATCH, and DELETE.
- [ ] Explain HTTP status codes 200, 301/302, 401, 403, 404, 500, 502, 503, and 504.
- [ ] Explain why HTTPS uses TLS.
- [ ] Inspect an HTTPS response with `curl -v`.
- [ ] Inspect a server certificate with `openssl s_client`.
- [ ] Compare a DNS resolution failure with an HTTP 404.
- [ ] Explain how DNS, a load balancer, a Kubernetes Service, and a Pod can fit into a request path.

## Knowledge check

1. What is the difference between an A record and a CNAME record?
2. What does a DNS TTL influence?
3. What is the difference between HTTP 404 and HTTP 503?
4. What does TLS provide for HTTPS?
5. Why is `curl -v` useful when troubleshooting a connection?

**Completion gate:** Complete the checklist and be able to explain where DNS resolution ends and the HTTP request begins. Then continue to [Topic 6 — Docker and Container Fundamentals](06-docker-container-fundamentals.md).
