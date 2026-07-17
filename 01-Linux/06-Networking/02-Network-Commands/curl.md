# 🌍 Linux `curl` Command

> Testing application communication through network protocols.

---

# 🎯 What Problem Does It Solve?

The `curl` command helps administrators answer:

> "Can my application communicate with this service?"

Unlike:

```bash
ping
```

which tests basic connectivity,

`curl` tests **application-level communication**.

It is commonly used for:

- HTTP/HTTPS testing
- REST API testing
- Downloading files
- Checking headers
- Debugging web services
- Testing Kubernetes services

---

# 🧩 Basic Syntax

General syntax:

```bash
curl [options] URL
```

Example:

```bash
curl https://example.com
```

---

# 🌐 Basic HTTP Request

Command:

```bash
curl http://example.com
```

Example output:

```html
<html>
<h1>Welcome</h1>
</html>
```

Meaning:

```text
Client

 |

HTTP Request

 |

Web Server

 |

HTTP Response
```

---

# 📋 Show HTTP Headers

Use:

```bash
curl -I https://example.com
```

Example:

```text
HTTP/2 200

server: nginx
content-type: text/html
```

Useful for checking:

- HTTP status
- Web server type
- Redirects
- TLS information

---

# 🔍 Verbose Mode

For detailed debugging:

```bash
curl -v https://example.com
```

Shows:

- DNS resolution
- TCP connection
- TLS handshake
- HTTP request
- HTTP response

Example:

```text
Connected to example.com

TLS handshake completed

GET / HTTP/2
```

---

# 🔐 Test HTTPS and TLS

Example:

```bash
curl https://harbor.example.com
```

Useful for:

- Certificate problems
- HTTPS connectivity
- Reverse proxy testing

Check certificate details:

```bash
curl -v https://example.com
```

Look for:

```text
SSL certificate verify ok
```

---

# 📥 Download Files

Download a file:

```bash
curl -O https://example.com/file.tar.gz
```

The file keeps its original name.

---

# 📝 Save Output to a File

Example:

```bash
curl https://example.com -o index.html
```

Creates:

```text
index.html
```

---

# 🔄 Follow Redirects

Many websites redirect requests.

Example:

```bash
curl -L http://example.com
```

Follows:

```text
HTTP 301

   |

   v

HTTPS URL
```

---

# 📡 Test REST APIs

Example:

```bash
curl https://api.example.com/users
```

Response:

```json
{
 "name":"admin"
}
```

---

## Send Data with POST

Example:

```bash
curl -X POST \
-H "Content-Type: application/json" \
-d '{"name":"linux"}' \
http://localhost:8080/api
```

Used for testing:

- APIs
- Microservices
- Web applications

---

# 🔑 Authentication Examples

Basic authentication:

```bash
curl -u username:password https://example.com
```

Bearer token:

```bash
curl \
-H "Authorization: Bearer TOKEN" \
https://api.example.com
```

---

# 🖥️ Real Linux Examples

## Example 1: Test nginx

Check if nginx responds:

```bash
curl http://localhost
```

Expected:

```html
Welcome to nginx
```

---

## Example 2: Test a remote service

```bash
curl http://192.168.1.20:8080
```

If successful:

```text
Application Response
```

---

## Example 3: Check Harbor

Example:

```bash
curl -k https://harbor.example.com
```

Useful for checking:

- DNS
- HTTPS
- Reverse proxy
- Certificate issues

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"Server is reachable but website does not work"

First:

```bash
ping server
```

Then:

```bash
ss -tulpn
```

Then:

```bash
curl -v http://server
```

Possible causes:

- Application failure
- Wrong HTTP configuration
- TLS problem

---

## Problem 2:
"Connection refused"

Example:

```bash
curl localhost:8080
```

Error:

```text
Connection refused
```

Meaning:

The host is reachable, but:

- Nothing is listening
- Service is stopped
- Wrong port

Check:

```bash
ss -tulpn
```

---

## Problem 3:
"DNS works but application fails"

Example:

```bash
dig app.example.com
```

Works.

But:

```bash
curl https://app.example.com
```

Fails.

Possible causes:

- Firewall
- TLS certificate
- Application issue

---

# ☸️ Kubernetes Connection

`curl` is one of the most useful Kubernetes troubleshooting tools.

Examples:

Test a Service:

```bash
curl http://service-name:8080
```

Test from inside a Pod:

```bash
kubectl exec -it pod-name -- curl service-name
```

Traffic flow:

```
Pod

 |

curl request

 |

Service

 |

Pod Endpoint

 |

Application
```

---

# 🌐 Ingress Testing

Example:

```bash
curl https://app.example.com
```

Tests:

```
DNS

 |

Ingress

 |

Service

 |

Pod

 |

Application
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `curl URL` | Send HTTP request |
| `curl -I URL` | Show headers |
| `curl -v URL` | Detailed debugging |
| `curl -L URL` | Follow redirects |
| `curl -O URL` | Download file |
| `curl -o file URL` | Save output |
| `curl -X POST` | Send POST request |
| `curl -k URL` | Ignore TLS certificate errors |

---

# Conclusion

The `curl` command connects networking concepts with real applications.

It allows administrators to test:

- Web servers
- APIs
- HTTPS
- Kubernetes services
- Microservices

Together with:

```text
ip → ping → ss → dig → curl
```

it provides a complete troubleshooting path from network configuration to application behavior.