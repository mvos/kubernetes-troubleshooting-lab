# Lab 06 — Kubernetes DNS / Cross-Namespace Service Discovery

## Scenario

The client runs in `default`, while `api-service` exists in the `backend` namespace.

Try:

```bash
kubectl exec dns-client -- nslookup api-service
```

The short name is searched relative to the client's namespace and will not resolve the Service in `backend`.

## Investigation

```bash
kubectl get svc -A
kubectl get pods -n kube-system
kubectl exec dns-client -- cat /etc/resolv.conf
kubectl exec dns-client -- nslookup kubernetes.default
kubectl exec dns-client -- nslookup api-service.backend
```

For deeper CoreDNS investigation:

```bash
kubectl get deployment -n kube-system coredns
kubectl logs -n kube-system -l k8s-app=kube-dns
kubectl get configmap -n kube-system coredns -o yaml
```

## Root cause

The application is using an incomplete DNS name for a Service in another namespace.

## Fix

Use either:

```text
api-service.backend
```

or the fully qualified cluster DNS name:

```text
api-service.backend.svc.cluster.local
```

Test connectivity:

```bash
kubectl exec dns-client -- wget -qO- http://api-service.backend
```

## Diagnostic lesson

Do not assume every name-resolution failure means CoreDNS is unhealthy. First verify the Service exists, its namespace, the client's DNS search domains, and whether known cluster names resolve.
