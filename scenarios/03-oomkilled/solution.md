# Lab 03 — OOMKilled

## Investigation

```bash
kubectl get pod oom-demo
kubectl describe pod oom-demo
kubectl logs oom-demo --previous
```

Inspect the last termination reason directly:

```bash
kubectl get pod oom-demo \
  -o jsonpath='{.status.containerStatuses[0].lastState.terminated.reason}'
```

Expected result:

```text
OOMKilled
```

The exit code commonly associated with a SIGKILL is `137`.

## Root cause

The container continuously allocates memory but has a `64Mi` memory limit. Once the container exceeds the cgroup limit, it is killed.

## Fix strategy

Do not automatically increase the limit. First determine whether the memory growth is expected or represents a leak.

Useful checks:

```bash
kubectl top pod oom-demo
kubectl get pod oom-demo -o yaml
kubectl describe node <node>
```

Possible remediation includes fixing a memory leak, tuning application caches/heaps, or setting a realistic request and limit based on observed usage.

## Interview takeaway

Distinguish a **container OOM** caused by its memory limit from **node memory pressure**, where the kubelet may evict workloads to protect the node.
