# 🌐 curl

> Transferring data, testing HTTP endpoints, inspecting headers, and downloading files from the command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Requests](#basic-requests)
- [Headers and Redirects](#headers-and-redirects)
- [Downloading Files](#downloading-files)
- [Sending Data](#sending-data)
- [Authentication](#authentication)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `curl URL` | Fetch response body |
| `curl -I URL` | Show headers only |
| `curl -i URL` | Show headers + body |
| `curl -L URL` | Follow redirects |
| `curl -O URL` | Save with remote filename |
| `curl -o FILE URL` | Save with custom filename |
| `curl -X METHOD URL` | Specify HTTP method |
| `curl -d DATA URL` | Send request body |
| `curl -H "Header: value"` | Add HTTP header |
| `curl -u USER:PASS URL` | Basic authentication |
| `curl -v URL` | Verbose/debug output |
| `curl -f URL` | Fail on HTTP 4xx/5xx |

---

## Overview

`curl` transfers data using many network protocols, but it is especially common with HTTP and HTTPS.

Typical DevOps uses:

```text
Test an API
Check service health
Inspect HTTP headers
Download files
Send JSON data
Troubleshoot connectivity
```

---

## Basic Requests

Fetch a page:

```bash
curl https://example.com
```

Include response headers:

```bash
curl -i https://example.com
```

Headers only:

```bash
curl -I https://example.com
```

Follow redirects:

```bash
curl -L https://example.com
```

---

## Headers and Redirects

Add a request header:

```bash
curl -H "Accept: application/json" \
https://api.example.com
```

Bearer token:

```bash
curl -H "Authorization: Bearer TOKEN" \
https://api.example.com
```

Verbose connection details:

```bash
curl -v https://example.com
```

Useful for checking:

```text
DNS resolution
TCP connection
TLS negotiation
HTTP request/response
```

---

## Downloading Files

Save using remote filename:

```bash
curl -O https://example.com/file.tar.gz
```

Choose filename:

```bash
curl -o package.tar.gz \
https://example.com/file.tar.gz
```

Resume a partial download:

```bash
curl -C - -O https://example.com/file.iso
```

---

## Sending Data

POST form-style data:

```bash
curl -d "name=alice&role=admin" \
https://example.com/users
```

Send JSON:

```bash
curl \
  -H "Content-Type: application/json" \
  -d '{"name":"alice"}' \
  https://api.example.com/users
```

Explicit method:

```bash
curl -X DELETE \
https://api.example.com/users/10
```

Often `curl` chooses the appropriate method automatically, so `-X` is not always required.

---

## Authentication

Basic authentication:

```bash
curl -u alice:password \
https://example.com/private
```

Avoid placing real secrets directly in shell history when possible.

---

## Practical Examples

Health check:

```bash
curl -fsS http://localhost:8080/health
```

Download and follow redirects:

```bash
curl -LO https://example.com/package.tar.gz
```

Show HTTP status only:

```bash
curl -s -o /dev/null \
-w "%{http_code}\n" \
https://example.com
```

Timeout after 5 seconds:

```bash
curl --max-time 5 https://example.com
```

---

## Common Pitfalls

### Redirects Not Followed

Use:

```bash
curl -L URL
```

### Binary Output in Terminal

Avoid:

```bash
curl https://example.com/file.zip
```

Prefer:

```bash
curl -O https://example.com/file.zip
```

### HTTP Error Still Returns Exit Code 0

By default, many HTTP errors still count as successful transfers.

For scripts, use:

```bash
curl -f
```

A common pattern is:

```bash
curl -fsS URL
```

### `-k` Disables Certificate Verification

This:

```bash
curl -k https://example.com
```

ignores TLS certificate verification.

Use only when you understand why verification is failing.

---

## Related Topics

- `wget.md`
- `curl-vs-wget.md`
- `../../../06-Networking/`
- `../../../08-Shell-Scripting/`

---

## Conclusion

For everyday DevOps work, the most valuable patterns are:

```bash
curl -I URL
curl -L URL
curl -O URL
curl -fsS URL
curl -H "Header: value" URL
```

`curl` is much more than a downloader; it is one of the most useful command-line tools for testing and troubleshooting HTTP services.