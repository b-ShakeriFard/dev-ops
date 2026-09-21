# ⚖️ curl vs wget

> Choosing the right tool for downloads, HTTP testing, APIs, and automated retrieval.

---

## On This Page

- [Quick Comparison](#quick-comparison)
- [Overview](#overview)
- [When to Use curl](#when-to-use-curl)
- [When to Use wget](#when-to-use-wget)
- [Common Tasks](#common-tasks)
- [Practical Examples](#practical-examples)
- [Conclusion](#conclusion)

---

## Quick Comparison

| Task | curl | wget |
|---|---:|---:|
| Download a file | ✅ | ✅ |
| Resume download | ✅ | ✅ |
| Follow redirects | `-L` | Usually automatic |
| HTTP headers | Excellent | Good |
| REST/API testing | Excellent | Limited |
| Send POST/JSON | Excellent | Possible, less convenient |
| Recursive download | Limited | Excellent |
| Website mirroring | No focus | Excellent |
| Batch downloads | Possible | Excellent |
| Multiple protocols | Excellent | Good |

---

## Overview

Both tools can download files, but they are optimized for different kinds of work.

A useful mental model is:

```text
curl
→ communicate with a server

wget
→ retrieve content from a server
```

There is significant overlap, so the choice depends on the task.

---

## When to Use curl

`curl` is especially useful when working with HTTP services.

Examples:

```text
Test an API
Check HTTP status
Inspect headers
Send JSON
Add authentication headers
Troubleshoot redirects
Perform health checks
```

Example:

```bash
curl -I https://example.com
```

Check an application:

```bash
curl -fsS http://localhost:8080/health
```

Send JSON:

```bash
curl \
  -H "Content-Type: application/json" \
  -d '{"name":"alice"}' \
  https://api.example.com/users
```

---

## When to Use wget

`wget` is usually more convenient when the primary goal is downloading.

Examples:

```text
Large ISO files
Packages
Archives
Interrupted downloads
Multiple files
Recursive websites
Documentation mirrors
```

Download:

```bash
wget https://example.com/file.iso
```

Resume:

```bash
wget -c https://example.com/file.iso
```

Recursive retrieval:

```bash
wget -r -np https://example.com/docs/
```

---

## Common Tasks

### Download and Keep Remote Filename

curl:

```bash
curl -O https://example.com/file.tar.gz
```

wget:

```bash
wget https://example.com/file.tar.gz
```

---

### Resume Download

curl:

```bash
curl -C - -O https://example.com/file.iso
```

wget:

```bash
wget -c https://example.com/file.iso
```

For large downloads, the `wget -c` form is particularly easy to remember.

---

### Save with Another Filename

curl:

```bash
curl -o app.tar.gz URL
```

wget:

```bash
wget -O app.tar.gz URL
```

Notice the difference:

```text
curl → -o
wget → -O
```

---

### Check HTTP Headers

curl:

```bash
curl -I https://example.com
```

wget:

```bash
wget --spider -S https://example.com
```

`curl` is usually more convenient for this kind of HTTP inspection.

---

## Practical Examples

### Large OpenShift ISO

```bash
wget -c URL
```

Why?

```text
Large file
+
Possible interrupted connection
+
Easy resume
```

### Kubernetes Health Endpoint

```bash
curl -fsS http://localhost:8080/health
```

Why?

```text
HTTP request
+
Exit code matters
+
No unnecessary output
```

### API Troubleshooting

```bash
curl -v https://api.example.com
```

Why?

It exposes useful details about:

```text
DNS
TCP
TLS
HTTP
```

---

## Key Difference

```text
curl
     ↓
Request / Response oriented

wget
     ↓
Download oriented
```

Neither tool is universally better.

They simply have different strengths.

---

## Related Topics

- `curl.md`
- `wget.md`
- `../../../06-Networking/`
- `../../../08-Shell-Scripting/`

---

## Conclusion

A practical rule of thumb:

```text
Need to test or interact with HTTP/API?
→ curl

Need to download, resume, or recursively retrieve files?
→ wget
```

For DevOps work, it is worth being comfortable with both.