# Kubernetes LEMP Stack Deployment - Interview Guide

## Overview

This challenge combines:

-   Secrets
-   ConfigMaps
-   Deployments
-   Multi-container Pods
-   Services
-   Environment variables
-   PHP configuration

Architecture:

    Browser
      |
    NodePort Service
      |
    Nginx/PHP Container
      |
    localhost:3306
      |
    MySQL Sidecar Container

In this lab MySQL runs in the same Pod as PHP, therefore the database
host is:

    127.0.0.1

------------------------------------------------------------------------

# Secrets

Secrets store sensitive values:

-   passwords
-   usernames
-   tokens

Create:

``` bash
kubectl create secret generic mysql-root-pass --from-literal=password=R00t
```

Multiple keys:

``` bash
kubectl create secret generic mysql-user-pass --from-literal=username=user --from-literal=password=password
```

Inspect keys:

``` bash
kubectl describe secret mysql-user-pass
```

Decode values:

``` bash
kubectl get secret mysql-user-pass -o jsonpath='{.data.password}' | base64 -d
```

------------------------------------------------------------------------

# Using Secrets as Environment Variables

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
    Container

Use `env` when defining individual variables.

------------------------------------------------------------------------

# ConfigMaps

ConfigMaps store non-sensitive configuration.

Example:

``` bash
kubectl create configmap php-config --from-literal=php.ini='variables_order = "EGPCS"'
```

Mount:

``` yaml
volumes:
- name: php-config-volume
  configMap:
    name: php-config
```

Container:

``` yaml
volumeMounts:
- name: php-config-volume
  mountPath: /opt/docker/etc/php/php.ini
  subPath: php.ini
```

------------------------------------------------------------------------

# Deployment

A Deployment manages Pods.

Example:

``` yaml
kind: Deployment
metadata:
  name: lemp-wp
```

This lab uses two containers:

-   nginx/php container
-   mysql container

------------------------------------------------------------------------

# Services

## NodePort

Exposes the website:

``` yaml
type: NodePort
```

Example:

``` yaml
ports:
- port: 80
  targetPort: 80
  nodePort: 30008
```

## ClusterIP

Provides internal networking:

``` yaml
type: ClusterIP
```

------------------------------------------------------------------------

# PHP Configuration

Never hardcode database details.

Bad:

``` php
$dbhost='127.0.0.1';
```

Good:

``` php
$dbhost=getenv('MYSQL_HOST');
$dbname=getenv('MYSQL_DATABASE');
$dbuser=getenv('MYSQL_USER');
$dbpass=getenv('MYSQL_PASSWORD');
```

Kubernetes injects the values.

------------------------------------------------------------------------

# Copy Application Files

Copy PHP file:

``` bash
kubectl cp /tmp/index.php POD:/app/index.php -c nginx-php-container
```

Verify:

``` bash
kubectl exec POD -c nginx-php-container -- cat /app/index.php
```

------------------------------------------------------------------------

# Troubleshooting

Check Pods:

``` bash
kubectl get pods
```

Describe:

``` bash
kubectl describe pod POD
```

Logs:

``` bash
kubectl logs POD -c container
```

Services:

``` bash
kubectl get svc
kubectl get endpoints
```

------------------------------------------------------------------------

# Interview Questions

## What is the difference between Secret and ConfigMap?

Secret stores sensitive information. ConfigMap stores normal
configuration.

## How does Kubernetes inject a Secret into a container?

Using:

``` yaml
env:
  valueFrom:
    secretKeyRef:
```

or by mounting it as a volume.

## Why should applications not contain passwords?

Because credentials should be separated from application code for
security and easier rotation.

## Why use Services instead of Pod IP addresses?

Pods are temporary. Services provide stable networking.

## Why can PHP connect using localhost?

Because MySQL is running in the same Pod and containers share the
network namespace.

## How would you troubleshoot database connection failures?

Check:

1.  Pod status
2.  Container logs
3.  Environment variables
4.  Service/endpoints
5.  Database availability

## What happens to MySQL data if the Pod is deleted?

Without persistent storage, data may be lost. Production systems use
PV/PVC.

## What Kubernetes objects were used?

-   Secret
-   ConfigMap
-   Deployment
-   Pod
-   Service
-   Environment variables
