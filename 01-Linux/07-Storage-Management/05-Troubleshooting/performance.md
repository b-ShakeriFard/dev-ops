# ⚡ Storage Performance Troubleshooting

> Diagnosing slow disks, high I/O wait, and storage bottlenecks.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Check I/O Wait](#check-io-wait)
- [Inspect Disk Performance](#inspect-disk-performance)
- [Find Busy Processes](#find-busy-processes)
- [Common Scenarios](#common-scenarios)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `top` | Check `%wa` I/O wait |
| `vmstat 1` | Monitor CPU and I/O pressure |
| `iostat -xz 1` | Inspect disk performance |
| `iotop` | Find processes generating I/O |
| `pidstat -d 1` | Show per-process disk I/O |
| `lsblk` | Identify storage devices |
| `df -h` | Check filesystem capacity |
| `journalctl -k` | Check kernel storage errors |

---

## Overview

Storage performance problems often appear as:

- Slow applications
- High load average
- Commands hanging
- Database latency
- Slow container startup
- High I/O wait

The goal is to answer:

```text
Is the disk busy?

Is one process generating excessive I/O?

Is the storage device slow or unhealthy?
```

---

## Check I/O Wait

Run:

```bash
top
```

Look for:

```text
wa
```

Example:

```text
%Cpu(s): 5.0 us, 2.0 sy, 40.0 wa
```

High `wa` means the CPU is spending significant time waiting for I/O.

Another useful command:

```bash
vmstat 1
```

Look at:

```text
wa
bi
bo
```

Where:

- `wa` = I/O wait
- `bi` = blocks read
- `bo` = blocks written

---

## Inspect Disk Performance

Install `sysstat` if needed:

```bash
sudo dnf install sysstat
```

Run:

```bash
iostat -xz 1
```

Important fields:

| Field | Meaning |
|---|---|
| `%util` | Device utilization |
| `await` | Average I/O wait time |
| `r/s` | Reads per second |
| `w/s` | Writes per second |
| `rkB/s` | Read throughput |
| `wkB/s` | Write throughput |

High values of:

```text
await
%util
```

can indicate a storage bottleneck.

---

## Find Busy Processes

Use:

```bash
sudo iotop
```

This shows which processes are generating disk I/O.

Alternative:

```bash
pidstat -d 1
```

Example:

```text
PID    kB_rd/s    kB_wr/s    Command
1234   100.00     5000.00    java
```

This immediately identifies the I/O-heavy process.

---

## Check Filesystem Capacity

A nearly full filesystem can also affect performance.

Check:

```bash
df -h
```

Check inode usage:

```bash
df -i
```

Very full filesystems should be investigated before blaming the disk itself.

---

## Check Kernel Errors

Storage problems may be hardware or backend related.

Check:

```bash
journalctl -k
```

or:

```bash
dmesg | tail -50
```

Look for:

```text
I/O error
timeout
reset
buffer error
device offline
```

---

## Common Scenarios

### High `%wa`

Check:

```bash
top
iostat -xz 1
iotop
```

Possible causes:

- Heavy database workload
- Large backup
- Log storm
- Slow disk
- Storage saturation

---

### One Process Uses Most Disk I/O

Check:

```bash
iotop
```

or:

```bash
pidstat -d 1
```

Then investigate that process.

Examples:

```text
database
backup
container runtime
logging service
```

---

### Disk Is 100% Utilized

Check:

```bash
iostat -xz 1
```

If:

```text
%util ≈ 100%
```

the device may be saturated.

Possible solutions:

- Reduce workload
- Spread I/O across disks
- Upgrade storage
- Tune application behavior

---

### Performance Suddenly Degraded

Check:

```bash
journalctl -k
```

Then:

```bash
iostat -xz 1
```

Possible causes:

- Storage errors
- SAN/NFS latency
- Failed disk
- Virtualization backend issue

---

### Container Workloads Are Slow

Check common storage paths:

```text
/var/lib/containers
/var/lib/docker
/var/lib/containerd
/var/lib/kubelet
```

Measure I/O:

```bash
iostat -xz 1
iotop
```

---

## Simple Troubleshooting Flow

```text
Application Slow
      ↓
top / vmstat
      ↓
High I/O wait?
      ↓
iostat
      ↓
Which disk?
      ↓
iotop / pidstat
      ↓
Which process?
      ↓
journalctl -k
      ↓
Storage healthy?
```

---

## Related Topics

- `disk-space.md`
- `filesystem-errors.md`
- `../03-LVM/resize.md`
- `../04-Mounting/troubleshooting.md`

---

## Conclusion

Storage performance troubleshooting usually comes down to three questions:

```text
Is the system waiting on I/O?

Which device is busy?

Which process is causing it?
```

The most useful commands are:

```bash
top
iostat -xz 1
iotop
pidstat -d 1
```