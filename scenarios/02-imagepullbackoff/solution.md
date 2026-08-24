# Lab 02 — ImagePullBackOff

## Investigation

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl get events --sort-by=.lastTimestamp
```

Pay special attention to the `Events` section. Kubernetes will report that it cannot pull the configured image.

## Root cause

The Deployment references the intentionally invalid image tag:

```text
nginx:this-tag-does-not-exist
```

`ErrImagePull` is typically seen first. After repeated failures Kubernetes increases the delay between attempts and the status becomes `ImagePullBackOff`.

## Fix

```bash
kubectl set image deployment/imagepull-demo web=nginx:1.27
kubectl rollout status deployment/imagepull-demo
```

## Production checks

For a real incident also validate:

- registry hostname and repository name
- image tag or digest
- `imagePullSecrets`
- workload identity / registry authentication
- network access to the registry
- registry rate limits or outage

## Verification

```bash
kubectl get pods
kubectl get deployment imagepull-demo
```
