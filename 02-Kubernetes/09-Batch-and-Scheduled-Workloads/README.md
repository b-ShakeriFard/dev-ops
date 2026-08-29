# Kubernetes Batch and Scheduled Workloads

Kubernetes can run workloads that are designed to **complete a task and exit**, rather than run continuously.

This section covers the main resources used for batch and scheduled execution:

- Jobs
- CronJobs
- Job completions
- Parallel Jobs
- Restart behavior
- Job history and cleanup
- Scheduling recurring workloads

A simple Job example:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: hello
          image: busybox:1.36
          command: ["sh", "-c", "echo Hello from Kubernetes"]

```

Jobs are useful for one-time tasks such as:

Database migrations
Reports
Data processing
Backup operations
Maintenance scripts

For recurring tasks, Kubernetes provides CronJob:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: scheduled-job
spec:
  schedule: "*/10 * * * *"

  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: Never
          containers:
            - name: task
              image: busybox:1.36
              command: ["sh", "-c", "date"]

```


And I’d structure that folder roughly like:

```text
09-Batch-and-Scheduled-Workloads/
├── README.md
├── Jobs.md
├── CronJobs.md
├── Parallel-Jobs.md
├── Job-Retries-and-Failures.md
└── Job-Cleanup.md
```
