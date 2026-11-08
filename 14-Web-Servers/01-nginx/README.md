# Nginx Lab

Hands-on Nginx exercises as part of my DevOps learning repository.

This project explores how Nginx serves websites, forwards requests to applications, handles HTTPS, and distributes traffic across multiple backends.

## Project status

Planning stage. The folders below describe the intended structure; configurations and walkthroughs will be added as each lab is completed.

## Learning roadmap

| Lab | Focus | Expected result |
| --- | --- | --- |
| 01 — Static website | Serve HTML and static assets | Open a custom page through Nginx |
| 02 — Reverse proxy | Forward requests to a backend application | Access the application through Nginx |
| 03 — HTTPS | Configure certificates and TLS termination | Access the application over HTTPS with certificate verification |
| 04 — Load balancing | Distribute requests between two backends | Observe responses from both backends |
| 05 — Nexus HTTPS | Apply reverse proxy and TLS concepts to Nexus | Access the Nexus UI and a container registry endpoint securely |

## Planned structure

```text
nginx/
├── README.md
├── 01-static-site/
├── 02-reverse-proxy/
├── 03-https/
├── 04-load-balancing/
├── 05-nexus-https/
└── docs/
```

Each lab will include its own README, configuration files, startup instructions, verification steps, and cleanup instructions. Container-based examples will use Docker or Podman, with runtime-specific differences documented where needed.

## Concepts covered

- Nginx configuration: `server`, `listen`, `server_name`, and `location`.
- Static content: `root`, `index`, and request paths.
- Reverse proxying: `proxy_pass` and forwarded headers.
- HTTPS: certificates, private keys, client trust, and HTTP redirects.
- Load balancing: upstream groups and backend availability.
- Operations: configuration validation, reloads, access logs, and error logs.
- Troubleshooting: connection failures, certificate errors, and HTTP 403, 404, 502, and 504 responses.

## Lab workflow

1. Understand the request flow and the purpose of each configuration.
2. Start Nginx and any required backend services.
3. Validate the configuration before reloading it.
4. Verify behavior using a browser, `curl`, and logs.
5. Record findings and troubleshooting steps.
6. Clean up the lab resources.

## Completion checklist

- [ ] Serve a custom static website.
- [ ] Proxy requests to an application.
- [ ] Enable HTTPS and configure client trust.
- [ ] Demonstrate traffic distribution across two backends.
- [ ] Configure HTTPS access to Nexus.
- [ ] Document a failure, its diagnosis, and its resolution.

## Repository hygiene

Keep private keys, credentials, tokens, and real environment secrets out of Git. Commit sanitized examples and document how to generate local certificates. Lab configurations require review before use in production.

## References

- [Nginx documentation](https://nginx.org/en/docs/)
- [Nginx beginner’s guide](https://nginx.org/en/docs/beginners_guide.html)

