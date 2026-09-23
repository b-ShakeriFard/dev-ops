# Kubernetes LEMP Stack - Complete Guide

## Challenge Statement

Deploy a LEMP-style application on Kubernetes.

Requirements:

-   Create MySQL Secrets:
    -   `mysql-root-pass`
        -   key: password
        -   value: R00t
    -   `mysql-user-pass`
        -   key: username
        -   value: kodekloud_pop
        -   key: password
        -   value: dCV3szSGNA
    -   `mysql-db-url`
        -   key: database
        -   value: kodekloud_db8
    -   `mysql-host`
        -   key: host
        -   value: 127.0.0.1
-   Create ConfigMap:
    -   name: `php-config`
    -   php.ini content: `variables_order = "EGPCS"`
-   Create Deployment:
    -   name: `lemp-wp`
-   Containers:
    -   `nginx-php-container`
        -   image: `webdevops/php-nginx:alpine-3-php7`
    -   `mysql-container`
        -   image: `mysql:5.6`
-   Mount ConfigMap:
    -   path: `/opt/docker/etc/php/php.ini`
-   Inject these environment variables from Secrets:

```{=html}
<!-- -->
```
    MYSQL_ROOT_PASSWORD
    MYSQL_DATABASE
    MYSQL_USER
    MYSQL_PASSWORD
    MYSQL_HOST

-   Create Services:
    -   NodePort: `lemp-service`
    -   nodePort: `30008`
    -   ClusterIP: `mysql-service`
    -   port: `3306`
-   Copy `/tmp/index.php` into:

```{=html}
<!-- -->
```
    /app/index.php

The PHP application must use environment variables, not hardcoded
database values.

Expected result:

    Connected successfully

------------------------------------------------------------------------

# Architecture

    User
     |
    NodePort 30008
     |
    Nginx/PHP Container
     |
    localhost:3306
     |
    MySQL Sidecar Container

------------------------------------------------------------------------

# Key Lessons Learned

## Same-Pod Communication

Containers in the same Pod share the network namespace.

Therefore MySQL can be reached through:

    127.0.0.1:3306

when MySQL is deployed as a sidecar.

------------------------------------------------------------------------

## Secret vs ConfigMap

  Object      Purpose
  ----------- --------------------------------
  Secret      Passwords, tokens, credentials
  ConfigMap   Non-sensitive configuration

------------------------------------------------------------------------

## Secret Injection

Pattern:

``` yaml
env:
  - name: MYSQL_PASSWORD
    valueFrom:
      secretKeyRef:
        name: mysql-user-pass
        key: password
```

Flow:

    Secret
     |
    secretKeyRef
     |
    Environment Variable
     |
    Application

------------------------------------------------------------------------

## Do Not Hardcode Configuration

Bad:

``` php
$dbhost = '127.0.0.1';
```

Good:

``` php
$dbhost = getenv('MYSQL_HOST');
```

Kubernetes injects configuration; the application reads it.

------------------------------------------------------------------------

## Troubleshooting Workflow

1.  Check Pods:

``` bash
kubectl get pods
```

2.  Check logs:

``` bash
kubectl logs POD -c container
```

3.  Verify environment variables:

``` bash
kubectl exec POD -- printenv
```

4.  Check Services:

``` bash
kubectl get svc
kubectl get endpoints
```

5.  Verify application configuration.

------------------------------------------------------------------------

# Interview Questions

## What is the difference between Secret and ConfigMap?

Secret stores sensitive data. ConfigMap stores normal configuration.

## How does Kubernetes inject Secrets into containers?

Using:

``` yaml
valueFrom:
  secretKeyRef:
```

or by mounting Secrets as files.

## Why should passwords not be stored in source code?

Because credentials can leak and rotation becomes difficult.

## Why does PHP use localhost instead of mysql-service?

Because MySQL is running in the same Pod.

## Why use Services?

Services provide stable networking because Pod IPs are temporary.

## How would you troubleshoot a database connection failure?

Check:

1.  Pod status
2.  Logs
3.  Environment variables
4.  Services/endpoints
5.  Database availability

## How would this change for production?

Use StatefulSets, PersistentVolumes, backups, and stronger security
controls.
