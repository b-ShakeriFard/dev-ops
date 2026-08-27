# Jobs 🛠️

## 1. Overview

A **Job** runs one or more Pods until a task completes successfully.

Unlike Deployments, which are designed for continuously running applications, Jobs are intended for **finite workloads** such as:

* Database migrations
* Batch processing
* Report generation
* Backups
* Data imports
* One-time administrative tasks

A Job tracks successful Pod completions and can retry failed Pods when required.

---

## 2. Job Workflow

```mermaid
flowchart LR
    JOB[🛠️ Job] --> POD1[🚀 Pod]
    POD1 -->|Success| DONE[✅ Job Complete]
    POD1 -->|Failure| RETRY[🔄 Retry Pod]
    RETRY --> POD2[🚀 New Pod]
    POD2 --> DONE
```

---

## 3. Main Concepts

| Concept         | Purpose                                  |
| --------------- | ---------------------------------------- |
| `completions`   | Number of successful executions required |
| `parallelism`   | Number of Pods allowed to run at once    |
| `backoffLimit`  | Maximum retry attempts before failure    |
| `restartPolicy` | Usually `Never` or `OnFailure`           |
| Job status      | Tracks success and failure counts        |

A Job is considered complete when its required number of successful completions has been reached.

---

## 4. Cheat Sheet

Create a Job:

```bash
kubectl create job hello \
  --image=busybox \
  -- echo "Hello Kubernetes"
```

List Jobs:

```bash
kubectl get jobs
```

Inspect:

```bash
kubectl describe job hello
```

View Pods created by the Job:

```bash
kubectl get pods -l job-name=hello
```

View logs:

```bash
kubectl logs job/hello
```

Delete:

```bash
kubectl delete job hello
```

Wait for completion:

```bash
kubectl wait \
  --for=condition=complete \
  job/hello \
  --timeout=60s
```

---

## 5. Practical Example

Suppose an administrator needs to process 10 independent data files.

Instead of running one long process, a Job can execute multiple Pods in parallel.

For example:

```text
completions: 10
parallelism: 2
```

Kubernetes runs up to two Pods at the same time until all 10 successful completions are reached.

This makes Jobs useful for batch workloads that can be divided into independent units.

---

## 6. YAML Example

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-processing
spec:
  completions: 5
  parallelism: 2
  backoffLimit: 3

  template:
    metadata:
      labels:
        app: batch-processing
    spec:
      restartPolicy: Never

      containers:
        - name: worker
          image: busybox:1.36
          command:
            - sh
            - -c
            - |
              echo "Processing task..."
              sleep 5
              echo "Task completed"
```

Apply it:

```bash
kubectl apply -f job.yaml
```

Monitor progress:

```bash
kubectl get jobs
kubectl get pods -l job-name=batch-processing
```

---

## 7. Common Problems 🚨

* Job repeatedly fails
* `backoffLimit` is reached
* Pod exits with a non-zero status
* Image cannot be pulled
* Restart policy is incorrect
* Job never reaches required completions
* Completed Jobs accumulate and consume API resources

---

## 8. Interview Questions 🎯

1. What is a Kubernetes Job?
2. How is a Job different from a Deployment?
3. What does `completions` control?
4. What does `parallelism` control?
5. What does `backoffLimit` do?
6. Which restart policies can Jobs commonly use?
7. How do you view logs from a Job?

---

## 9. Related Topics 🔗

* CronJob
* Pods
* Batch Processing
* Restart Policy
* Parallel Jobs
* Job Cleanup
