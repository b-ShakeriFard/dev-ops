# Kubernetes Managing Environment Variables (Downward API)

## Overview

This challenge demonstrates Kubernetes environment variables using the
**Downward API**.

The Pod exposes its own metadata and runtime information to the
container:

-   Node name
-   Pod name
-   Pod IP
-   Service account name

Flow:

``` text
Kubernetes Pod
      |
      | valueFrom.fieldRef
      |
      +--> NODE_NAME
      +--> POD_NAME
      +--> POD_IP
      +--> POD_SERVICE_ACCOUNT
```

## Key Concepts

### command vs args

Kubernetes separates the executable from its arguments:

``` yaml
command:
  - sh
  - -c

args:
  - |
    while true; do
      echo "";
      printenv NODE_NAME POD_NAME;
      sleep 10;
    done;
```

`command` defines what runs.

`args` provides parameters or scripts.

## Complete Pod Example

``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: envars
  labels:
    app: web-app

spec:
  containers:
    - name: fieldref-container
      image: redis:latest

      command:
        - sh
        - -c

      args:
        - |
          while true; do
            echo "";
            printenv NODE_NAME POD_NAME;
            printenv POD_IP POD_SERVICE_ACCOUNT;
            sleep 10;
          done;

      env:
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName

        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name

        - name: POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP

        - name: POD_SERVICE_ACCOUNT
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName

  restartPolicy: Never
```

## Validation

Apply:

``` bash
kubectl apply --dry-run=server -f pod.yml
kubectl apply -f pod.yml
```

Check status:

``` bash
kubectl get pod envars
```

Inspect environment variables:

``` bash
kubectl exec envars -- printenv
```

Specific variables:

``` bash
kubectl exec envars -- printenv NODE_NAME POD_NAME POD_IP POD_SERVICE_ACCOUNT
```

## Lessons Learned

-   `value: spec.nodeName` is just a string; it does not query
    Kubernetes.
-   Use `valueFrom.fieldRef` to inject Pod information.
-   `metadata.name` gives the Pod name.
-   `status.podIP` becomes available after scheduling.
-   `spec.serviceAccountName` exposes the assigned ServiceAccount.
-   YAML indentation is critical.
