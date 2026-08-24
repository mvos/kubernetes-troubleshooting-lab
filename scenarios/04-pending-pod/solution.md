# Lab 04 — Pod Stuck in Pending

## Investigation

```bash
kubectl get pod pending-demo
kubectl describe pod pending-demo
kubectl get events --sort-by=.lastTimestamp
```

A scheduling failure should appear in the events, typically indicating that no node has enough CPU for the request.

Check node capacity and current allocation:

```bash
kubectl get nodes
kubectl describe nodes
kubectl top nodes
```

## Root cause

The Pod requests `1000` CPU cores, intentionally making it unschedulable on a normal lab cluster.

Remember that:

```text
1000m = 1 CPU core
1000  = 1000 CPU cores
```

## Fix

Change the request to a realistic value such as:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "64Mi"
```

## Other causes of Pending Pods

A Pending Pod can also result from node selectors, affinity/anti-affinity, taints and tolerations, unbound PVCs, topology constraints, quotas, or unavailable capacity.

The scheduler event is usually the fastest way to narrow the problem.
