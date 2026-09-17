# Kubernetes LAMP Deployment with Secrets and ConfigMap

This challenge deploys Apache/PHP and MySQL as two containers in one Kubernetes Pod. Database settings are loaded from existing Secrets into environment variables. The PHP application reads those variables at runtime, so credentials and connection details are never hardcoded in `index.php`.

## Requirements

| Resource | Required value |
|---|---|
| ConfigMap | `php-config` |
| Deployment | `lamp-wp` |
| PHP container | `httpd-php-container` |
| PHP image | `webdevops/php-apache:alpine-3-php7` |
| MySQL container | `mysql-container` |
| MySQL image | `mysql:5.6` |
| Web Service | `lamp-service`, NodePort `30008` |
| MySQL Service | `mysql-service`, port `3306` |
| Apache document root | `/app` |
| PHP configuration path | `/opt/docker/etc/php/php.ini` |

## 1. Inspect the existing Secrets

List Secret names and their keys without decoding or displaying their values:

```bash
kubectl get secrets -o go-template='{{range .items}}{{.metadata.name}}{{": "}}{{range $key, $value := .data}}{{$key}}{{" "}}{{end}}{{"\n"}}{{end}}'
```

The challenge provides this mapping:

| Environment variable | Secret | Key |
|---|---|---|
| `MYSQL_ROOT_PASSWORD` | `mysql-root-pass` | `password` |
| `MYSQL_DATABASE` | `mysql-db-url` | `database` |
| `MYSQL_USER` | `mysql-user-pass` | `username` |
| `MYSQL_PASSWORD` | `mysql-user-pass` | `password` |
| `MYSQL_HOST` | `mysql-host` | `host` |

## 2. Create the ConfigMap

The key must be named `php.ini`, because it will be mounted as that file:

```bash
kubectl create configmap php-config \
  --from-literal=php.ini='variables_order = "EGPCS"'
```

## 3. Create the Deployment

Save the following as `deploy.yml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lamp-wp
  labels:
    app: lamp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lamp-wp
  template:
    metadata:
      labels:
        app: lamp-wp
    spec:
      containers:
        - name: httpd-php-container
          image: webdevops/php-apache:alpine-3-php7
          ports:
            - containerPort: 80
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-root-pass
                  key: password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-db-url
                  key: database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: username
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: password
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-host
                  key: host
          volumeMounts:
            - name: php-config-volume
              mountPath: /opt/docker/etc/php/php.ini
              subPath: php.ini

        - name: mysql-container
          image: mysql:5.6
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-root-pass
                  key: password
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-db-url
                  key: database
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: username
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: password
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-host
                  key: host

      volumes:
        - name: php-config-volume
          configMap:
            name: php-config
```

Validate the YAML before creating anything:

```bash
kubectl apply --dry-run=server -f deploy.yml
```

Then apply it:

```bash
kubectl apply -f deploy.yml
kubectl rollout status deployment/lamp-wp
```

The name under `volumeMounts` must exactly match the name under `volumes`. Also, `volumes` belongs under the Pod `spec`, at the same indentation level as `containers`.

## 4. Create the Services

Save this as `services.yml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  type: NodePort
  selector:
    app: lamp-wp
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  type: ClusterIP
  selector:
    app: lamp-wp
  ports:
    - name: mysql
      protocol: TCP
      port: 3306
      targetPort: 3306
```

Apply and inspect them:

```bash
kubectl apply --dry-run=server -f services.yml
kubectl apply -f services.yml
kubectl get svc lamp-service mysql-service
kubectl get endpoints lamp-service mysql-service
```

## 5. Prepare `index.php`

Copy the supplied file into the home directory before editing it. `/tmp/index.php` may not be writable by the current user.

```bash
cp /tmp/index.php ~/index.php
```

Replace its contents with the following code:

```bash
cat > ~/index.php <<'PHP'
<?php
$dbname = getenv('MYSQL_DATABASE');
$dbuser = getenv('MYSQL_USER');
$dbpass = getenv('MYSQL_PASSWORD');
$dbhost = getenv('MYSQL_HOST');

$connect = mysqli_connect($dbhost, $dbuser, $dbpass, $dbname);

if ($connect === false) {
    die('Connection failed: ' . mysqli_connect_error());
}

$result = mysqli_query($connect, 'SHOW TABLES');

if ($result === false) {
    die('Query failed: ' . mysqli_error($connect));
}

echo 'Connected successfully';

mysqli_free_result($result);
mysqli_close($connect);
?>
PHP
```

The code uses `getenv()` for all database settings. No Secret value appears in the PHP source.

## 6. Copy the application into the container

Wait until both containers are running:

```bash
kubectl get pods
```

Expected readiness is `2/2`. Then stream the local file into `/app/index.php`:

```bash
kubectl exec -i deployment/lamp-wp \
  -c httpd-php-container -- \
  sh -c 'cat > /app/index.php' < ~/index.php
```

This avoids depending on `kubectl cp` and the container's `tar` command.

## 7. Validate before clicking Check

Check the PHP syntax:

```bash
kubectl exec deployment/lamp-wp \
  -c httpd-php-container -- \
  php -l /app/index.php
```

Confirm that the file exists and contains environment-variable lookups:

```bash
kubectl exec deployment/lamp-wp \
  -c httpd-php-container -- \
  sh -c 'ls -l /app/index.php && grep getenv /app/index.php'
```

Check the final resources:

```bash
kubectl get pods
kubectl get deployment lamp-wp
kubectl get svc lamp-service mysql-service
kubectl get endpoints lamp-service mysql-service
```

Open NodePort `30008`. The page must display:

```text
Connected successfully
```

Only then click **Check**.

## Troubleshooting lessons

### Browser displays `Index of /`

Apache and the NodePort Service are working, but `/app/index.php` is missing. Copy the file into the HTTP container and refresh the page.

### Pod remains in `ContainerCreating`

Logs are unavailable until the container starts. Inspect Pod events instead:

```bash
kubectl describe pod -l app=lamp-wp
```

Common causes include a missing ConfigMap, an invalid volume mount, or a volume name mismatch.

### YAML parsing error

Display the suspicious region with line numbers:

```bash
nl -ba deploy.yml | sed -n '20,70p'
```

Then run the server-side dry run again.

### Application cannot connect to MySQL

```bash
kubectl logs deployment/lamp-wp -c httpd-php-container --tail=30
kubectl logs deployment/lamp-wp -c mysql-container --tail=30
kubectl get svc mysql-service
kubectl get endpoints mysql-service
```

Confirm that each `secretKeyRef` uses the exact Secret name and key, and that `mysql-service` has an endpoint.

### Fast interview workflow

For repeated environment-variable blocks, write one correct five-entry block, validate its indentation, then duplicate it under the second container. Use `kubectl apply --dry-run=server` after each major section. This catches structural errors before the workload is created and is faster than debugging several mistakes at once.

