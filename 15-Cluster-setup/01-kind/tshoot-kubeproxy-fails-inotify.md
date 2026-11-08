# Troubleshooting kind: CrashLoopBackOff and inotify limits

## Environment and symptoms

A single-node kind cluster running on Podman inside an Ubuntu VirtualBox VM had two pods in `CrashLoopBackOff`:

- `kube-proxy` in `kube-system`
- `local-path-provisioner` in `local-path-storage`

`CrashLoopBackOff` means a container repeatedly exits and Kubernetes waits progressively longer between restart attempts. It describes the restart behavior; the logs reveal the cause.

The kube-proxy log contained:

```text
failed complete: fsnotify watcher init: too many open files
```

The provisioner log included:

```text
invalid empty flag helper-pod-file ... ConfigMap local-path-storage/local-path-config
... dial tcp 10.96.0.1:443: i/o timeout
```

The key detail in the second error was the API connection timeout. It did not prove that the ConfigMap was missing.

## What is inotify?

inotify is a Linux kernel feature that lets applications receive notifications when files or directories change, instead of repeatedly checking them (polling).

For example, a program can monitor its configuration file and receive an event when it is modified, moved, or deleted. The program decides how to respond; inotify does not reload configuration itself.

| Term or setting | Meaning |
| --- | --- |
| Instance | A notification channel with an event queue, accessed through a file descriptor |
| Watch | A file or directory monitored through an instance; one instance can hold many watches |
| `fs.inotify.max_user_instances` | Maximum inotify instances per real user ID |
| `fs.inotify.max_user_watches` | Maximum watches per real user ID, across its instances |
| File descriptor | A numbered handle used by a process to access a resource, including an inotify instance |

Watching a directory does not automatically watch every subdirectory recursively.

### Why did the error say “too many open files”?

Creating an inotify instance can return `EMFILE` when either the user's instance limit or the process's file descriptor limit is reached. Therefore, this message does not necessarily mean too many ordinary files are open.

kind nodes are containers sharing their Linux host's kernel. Host resource limits can affect workloads inside them. Here, the relevant host was the **Ubuntu VM running Podman**, not the physical machine running VirtualBox.

## Diagnosis

Run these read-only checks on the Ubuntu VM:

```bash
kubectl get pods -A
cat /proc/sys/fs/inotify/max_user_instances
cat /proc/sys/fs/inotify/max_user_watches
cat /proc/sys/fs/file-nr
ulimit -n
podman ps --format 'table {{.Names}}\t{{.Status}}'
```

Read the failing container logs, replacing pod names with current names:

```bash
kubectl logs -n kube-system <kube-proxy-pod> --previous --tail=60
kubectl logs -n local-path-storage <provisioner-pod> --previous --tail=60
```

If there is no previous container, omit `--previous`.

Observed values:

| Check | Result | Interpretation |
| --- | --- | --- |
| `max_user_instances` | `128` | A candidate constraint; this value alone does not prove exhaustion |
| `max_user_watches` | `25229` | A separate limit; it was left unchanged |
| `/proc/sys/fs/file-nr` | `9760 0 9223372036854775807` | No evidence of system-wide file table exhaustion |
| Host shell `ulimit -n` | `1024` | The shell's soft descriptor limit; not proof of the pod's limit |

The watcher initialization error pointed toward instance or descriptor limits. Raising only the instance limit restored the pods, providing strong evidence that instance exhaustion was the immediate constraint. We did not identify which processes consumed the original allowance.

### Why did the provisioner fail too?

kube-proxy normally programs networking rules for Kubernetes Services. Its failure was consistent with the provisioner being unable to reach the API through the Service IP `10.96.0.1:443`.

Both pods recovered after the limit change, supporting a shared failure chain: instance exhaustion prevented kube-proxy startup, and broken Service connectivity prevented provisioner initialization. A kube-proxy crash does not always break all Service traffic immediately, because existing rules may remain.

## Temporary fix — validated in this incident

Run on the Ubuntu VM, outside the kind container:

```bash
echo 1024 | sudo tee /proc/sys/fs/inotify/max_user_instances
```

This raises the allowance immediately from 128 to 1024. It permits additional kernel memory use as applications create more instances; it does not preallocate all those instances. This was sufficient for this lab, rather than a universal sizing recommendation.

No cluster rebuild or restart was needed. Kubernetes retried the containers automatically:

```bash
kubectl get pods -A -w
```

Press `Ctrl+C` to stop watching. Allow several minutes for restart backoff.

**Outcome:** the user confirmed recovery after this change. The direct `/proc` setting does not persist across reboot.

## Persistent fix

After validating the temporary change, store the setting in a dedicated file. If this file already exists, inspect it before replacing its contents:

```bash
echo 'fs.inotify.max_user_instances = 1024' | 
  sudo tee /etc/sysctl.d/99-kind-inotify.conf
```

The active value is already set by the temporary fix. On Ubuntu, the configuration is loaded at boot. To explicitly apply just this file now:

```bash
sudo /usr/sbin/sysctl -p /etc/sysctl.d/99-kind-inotify.conf
```

If `sysctl` is unavailable even at that path, it is provided by Ubuntu's `procps` package. The direct `/proc` command above still applies the current value.

Verify now, and again after the next planned reboot:

```bash
cat /proc/sys/fs/inotify/max_user_instances
kubectl get nodes
kubectl get pods -A
```

Expected: the value is `1024`, the node is `Ready`, and the affected pods are `Running` and ready. Historical restart counts do not reset; check that they stop increasing. Persistence was not tested during the original incident.

## Rollback

If you created the dedicated file above, remove it and restore the original runtime value:

```bash
sudo rm /etc/sysctl.d/99-kind-inotify.conf
echo 128 | sudo tee /proc/sys/fs/inotify/max_user_instances
```

If the file existed before this change, restore its previous contents instead. Returning to 128 may cause the original startup failures to recur.

## If the problem continues

Check fresh logs and the kind node container's shell descriptor limit:

```bash
podman exec kind-first-cluster-control-plane sh -c 'ulimit -n'
kubectl get events -A --sort-by=.metadata.creationTimestamp
```

That shell limit is a useful clue, but inspecting the affected process's `/proc/<PID>/limits` gives its actual limits. Do not assume every “too many open files” error has the same cause, or keep increasing limits without investigating usage and possible leaks.

## References

- [kind: Pod errors due to too many open files](https://kind.sigs.k8s.io/docs/user/known-issues/#pod-errors-due-to-too-many-open-files)
- [Linux manual: inotify overview and limits](https://man7.org/linux/man-pages/man7/inotify.7.html)
- [Linux manual: inotify initialization and error meanings](https://man7.org/linux/man-pages/man2/inotify_init.2.html)
