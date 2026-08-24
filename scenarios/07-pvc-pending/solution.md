# Lab 07 — PVC Pending

## Investigation

```bash
kubectl get pvc
kubectl describe pvc data-pvc
kubectl get storageclass
kubectl describe pod pvc-demo
kubectl get events --sort-by=.lastTimestamp
```

## Root cause

The PVC explicitly requests:

```text
storage-class-that-does-not-exist
```

No matching StorageClass exists, so dynamic provisioning cannot occur and the claim remains Pending.

## Fix

List available StorageClasses:

```bash
kubectl get storageclass
```

Update the PVC to use a StorageClass supported by your cluster. PVC fields related to storage class are generally not something you should expect to freely mutate after creation, so for a lab the simplest approach is to recreate the claim with the correct configuration.

```bash
kubectl delete pod pvc-demo
kubectl delete pvc data-pvc
```

Then modify `storageClassName` in the manifest and apply it again.

## Production checks

Also investigate:

- CSI controller health
- CSI node plugins
- cloud/provider storage quotas
- topology constraints
- requested access modes
- requested capacity
- `volumeBindingMode`
- PV availability when using static provisioning

## Verification

```bash
kubectl get pvc
kubectl get pv
kubectl get pod pvc-demo
```

The PVC should become `Bound` before the Pod can use it.
