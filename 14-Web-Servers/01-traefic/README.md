traefik-gateway/
│
├── README.md
│
├── architecture/
│   ├── diagrams/
│   └── design-decisions.md
│
├── deployment/
│   └── helm/
│
├── applications/
│   ├── flask/
│   ├── nginx/
│   └── backend-c/
│
├── ingress/
│   ├── ingress/
│   ├── ingressroute/
│   └── gateway-api/
│
├── security/
│   ├── tls/
│   └── middleware/
│
├── monitoring/
│   ├── prometheus/
│   └── grafana/
│
├── tests/
│   ├── routing.sh
│   ├── tls.sh
│   └── load-balancing.sh
│
└── docs/
    ├── troubleshooting.md
    └── production-readiness.md
