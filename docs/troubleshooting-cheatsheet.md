# Kubernetes Troubleshooting Cheat Sheet

## Cluster overview

```bash
kubectl cluster-info
kubectl get nodes -o wide
kubectl get pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

## Pod troubleshooting

```bash
kubectl get pod <pod> -o wide
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl logs <pod> -c <container>
kubectl get pod <pod> -o yaml
```

Useful status extraction:

```bash
kubectl get pod <pod> \
  -o jsonpath='{range .status.containerStatuses[*]}{.name}{" ready="}{.ready}{" restarts="}{.restartCount}{"\n"}{end}'
```

## Deployment / rollout

```bash
kubectl get deployment
kubectl describe deployment <deployment>
kubectl rollout status deployment/<deployment>
kubectl rollout history deployment/<deployment>
kubectl get rs
```

## Networking

```bash
kubectl get svc -A
kubectl get endpoints -A
kubectl get endpointslices -A
kubectl get networkpolicy -A
kubectl get pods -A -o wide
```

Test from inside the cluster:

```bash
kubectl run net-debug --rm -it --restart=Never \
  --image=curlimages/curl -- sh
```

## DNS

```bash
kubectl exec <pod> -- cat /etc/resolv.conf
kubectl exec <pod> -- nslookup kubernetes.default
kubectl get pods -n kube-system
kubectl logs -n kube-system -l k8s-app=kube-dns
```

## Resources

```bash
kubectl top nodes
kubectl top pods -A
kubectl describe node <node>
kubectl get resourcequota -A
kubectl get limitrange -A
```

## Storage

```bash
kubectl get pvc -A
kubectl get pv
kubectl get storageclass
kubectl describe pvc <pvc>
kubectl get csidrivers
kubectl get csinodes
```

## Scheduling

```bash
kubectl describe pod <pending-pod>
kubectl get nodes --show-labels
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
```

Check:

- CPU/memory requests
- node selectors
- affinity / anti-affinity
- taints / tolerations
- topology constraints
- PVC binding
- quotas

## Node health

```bash
kubectl get nodes
kubectl describe node <node>
kubectl get pods -A -o wide --field-selector spec.nodeName=<node>
kubectl top node <node>
```

## Debugging mindset

Prefer evidence over assumptions:

1. Reproduce or clearly define the symptom.
2. Establish scope: one Pod, one node, one namespace, or cluster-wide?
3. Check recent events.
4. Compare desired state with observed state.
5. Follow dependencies one layer at a time.
6. Change one thing at a time when possible.
7. Verify both technical recovery and application health.
8. Document the root cause and prevention action.
