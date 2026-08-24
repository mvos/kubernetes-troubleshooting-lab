# Lab 08 — NodeNotReady

Unlike the previous labs, this scenario is an **investigation runbook** rather than a manifest that intentionally damages a node. It is designed to demonstrate safe production troubleshooting.

> Do not intentionally stop kubelet, fill disks, or break networking on shared or production clusters.

## Symptom

```bash
kubectl get nodes
```

A node reports `NotReady`, or workloads on the node are being evicted or becoming unavailable.

## Kubernetes-side investigation

```bash
kubectl get nodes -o wide
kubectl describe node <node>
kubectl get pods -A -o wide --field-selector spec.nodeName=<node>
kubectl get events -A --sort-by=.lastTimestamp
kubectl top node <node>
```

Inspect node conditions:

```bash
kubectl get node <node> \
  -o jsonpath='{range .status.conditions[*]}{.type}{"="}{.status}{" reason="}{.reason}{"\n"}{end}'
```

Important conditions include:

- `Ready`
- `MemoryPressure`
- `DiskPressure`
- `PIDPressure`
- `NetworkUnavailable`

## Node-side investigation

If you have OS access:

```bash
systemctl status kubelet
journalctl -u kubelet --since "30 minutes ago"
df -h
df -i
free -m
ip addr
ip route
```

Depending on the runtime:

```bash
crictl info
crictl ps -a
crictl images
```

## Common root causes

- kubelet stopped or unhealthy
- container runtime failure
- disk or inode exhaustion
- memory pressure
- CNI/network failure
- node-to-control-plane connectivity failure
- certificate problems
- cloud VM or infrastructure failure

## Safe response pattern

For an unhealthy production node, consider preventing additional scheduling while investigating:

```bash
kubectl cordon <node>
```

If the node must be removed for maintenance, evaluate disruption budgets and workload impact before draining it:

```bash
kubectl drain <node> --ignore-daemonsets
```

Do not blindly drain a node during an incident. Stateful workloads, local storage, PodDisruptionBudgets, capacity and application redundancy must be evaluated first.

## Verification

After fixing the underlying problem:

```bash
kubectl get node <node>
kubectl describe node <node>
```

If it was cordoned and is safe to return to service:

```bash
kubectl uncordon <node>
```

## Interview takeaway

`NodeNotReady` is a symptom. A strong support engineer correlates Kubernetes node conditions and events with kubelet, runtime, OS, network and infrastructure evidence before deciding on remediation.
