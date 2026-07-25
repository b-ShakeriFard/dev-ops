## Complete kubectl Command Reference

| Command                 | Short description                                                  |
| ----------------------- | ------------------------------------------------------------------ |
| `kubectl annotate`      | Add, update, or remove annotations on resources.                   |
| `kubectl api-resources` | List resource types supported by the API server.                   |
| `kubectl api-versions`  | List API group/version combinations supported by the server.       |
| `kubectl apply`         | Create or update resources from YAML, JSON, or standard input.     |
| `kubectl attach`        | Attach your terminal to a running container process.               |
| `kubectl auth`          | Inspect permissions and Kubernetes authorization.                  |
| `kubectl autoscale`     | Create a HorizontalPodAutoscaler for a workload.                   |
| `kubectl certificate`   | Approve or deny certificate-signing requests.                      |
| `kubectl cluster-info`  | Display control-plane and cluster service information.             |
| `kubectl completion`    | Generate shell-completion code for Bash, Zsh, Fish, or PowerShell. |
| `kubectl config`        | View and modify kubeconfig clusters, users, and contexts.          |
| `kubectl cordon`        | Mark a node as unschedulable.                                      |
| `kubectl cp`            | Copy files between the local system and containers.                |
| `kubectl create`        | Create resources from files, input, or command arguments.          |
| `kubectl debug`         | Create debugging containers, Pods, or node sessions.               |
| `kubectl delete`        | Delete resources by name, selector, or manifest.                   |
| `kubectl describe`      | Display detailed resource information and related events.          |
| `kubectl diff`          | Compare live resources with proposed manifest changes.             |
| `kubectl drain`         | Safely evict workloads from a node before maintenance.             |
| `kubectl edit`          | Edit a live Kubernetes resource using a text editor.               |
| `kubectl events`        | Display Kubernetes events.                                         |
| `kubectl exec`          | Execute a command inside a running container.                      |
| `kubectl explain`       | Display built-in documentation for resources and fields.           |
| `kubectl expose`        | Create a Service that exposes a workload or Pod.                   |
| `kubectl get`           | List or display Kubernetes resources.                              |
| `kubectl kuberc`        | View or modify kubectl user-preference configuration.              |
| `kubectl kustomize`     | Build Kubernetes manifests from a Kustomize directory.             |
| `kubectl label`         | Add, update, or remove labels on resources.                        |
| `kubectl logs`          | Display container logs from a Pod.                                 |
| `kubectl options`       | Display flags inherited by all kubectl commands.                   |
| `kubectl patch`         | Partially update selected fields of a resource.                    |
| `kubectl plugin`        | Inspect kubectl plugins available through the system `PATH`.       |
| `kubectl port-forward`  | Forward local ports to a Pod, Service, or workload.                |
| `kubectl proxy`         | Run a local proxy to the Kubernetes API server.                    |
| `kubectl replace`       | Replace an existing resource using a manifest.                     |
| `kubectl rollout`       | Manage workload rollouts, history, status, and rollback.           |
| `kubectl run`           | Create and run a Pod from a container image.                       |
| `kubectl scale`         | Change the replica count of a workload.                            |
| `kubectl set`           | Update selected workload settings such as images or resources.     |
| `kubectl taint`         | Add, update, or remove taints on nodes.                            |
| `kubectl top`           | Display CPU and memory usage for Pods or nodes.                    |
| `kubectl uncordon`      | Mark a node as schedulable again.                                  |
| `kubectl version`       | Display kubectl client and Kubernetes server versions.             |
| `kubectl wait`          | Wait until a resource reaches a specified condition.               |

### Important Command Groups

| Command group          | Common subcommands                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `kubectl apply`        | `edit-last-applied`, `set-last-applied`, `view-last-applied`                                                                                  |
| `kubectl auth`         | `can-i`, `reconcile`, `whoami`                                                                                                                |
| `kubectl certificate`  | `approve`, `deny`                                                                                                                             |
| `kubectl cluster-info` | `dump`                                                                                                                                        |
| `kubectl config`       | `current-context`, `get-contexts`, `use-context`, `set-context`, `view`, `set-cluster`, `set-credentials`, `rename-context`, `delete-context` |
| `kubectl create`       | `deployment`, `service`, `namespace`, `configmap`, `secret`, `job`, `cronjob`, `role`, `rolebinding`, `clusterrole`, `serviceaccount`         |
| `kubectl plugin`       | `list`                                                                                                                                        |
| `kubectl rollout`      | `history`, `pause`, `restart`, `resume`, `status`, `undo`                                                                                     |
| `kubectl set`          | `env`, `image`, `resources`, `selector`, `serviceaccount`, `subject`                                                                          |
| `kubectl top`          | `node`, `pod`                                                                                                                                 |

To display the commands supported by the locally installed version:

```bash
kubectl --help
```

To inspect a particular command:

```bash
kubectl <command> --help
```

Example:

```bash
kubectl rollout --help
```
