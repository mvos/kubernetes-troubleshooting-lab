# Lab 05 — Service Connectivity

## Symptom

The Pods are Running but requests through `web-service` fail.

## Investigation

```bash
kubectl get pods --show-labels
kubectl get svc web-service
kubectl describe svc web-service
kubectl get endpoints web-service
kubectl get endpointslices -l kubernetes.io/service-name=web-service
```

The key observation is that the Service has no backend endpoints.

Compare the Service selector with Pod labels:

```bash
kubectl get svc web-service -o jsonpath='{.spec.selector}'
kubectl get pods --show-labels
```

## Root cause

Pods have:

```text
app=web-demo
```

The Service searches for:

```text
app=web-prod
```

Therefore no Pods match the selector.

## Fix

```bash
kubectl patch service web-service \
  -p '{"spec":{"selector":{"app":"web-demo"}}}'
```

## Verification

```bash
kubectl get endpoints web-service
kubectl run curl-test --rm -it --restart=Never \
  --image=curlimages/curl -- http://web-service
```

## Diagnostic lesson

When a Service is unreachable, troubleshoot the path layer by layer:

```text
Client → DNS → Service → EndpointSlice → Pod IP → container port → application
```
