# Lab 01 — CrashLoopBackOff

## Symptom

The container starts, exits with an error, and Kubernetes continuously restarts it.

```bash
kubectl get pod crashloop-demo
```

Expected status after several restarts:

```text
CrashLoopBackOff
```

## Investigation

Start with the Pod details and events:

```bash
kubectl describe pod crashloop-demo
```

Then inspect the application output:

```bash
kubectl logs crashloop-demo
```

When a container has already restarted, the previous instance is often more useful:

```bash
kubectl logs crashloop-demo --previous
```

Inspect termination information:

```bash
kubectl get pod crashloop-demo \
  -o jsonpath='{.status.containerStatuses[0].lastState.terminated}'
```

## Root cause

The application exits with status `1` because the required `APP_MODE` configuration is empty.

## Fix

Change the environment variable to a valid value and make the process remain healthy. For example:

```yaml
env:
  - name: APP_MODE
    value: "production"
```

In a real application the fix could involve a ConfigMap, Secret, command argument, missing dependency, filesystem permission, or application configuration.

## Verification

```bash
kubectl get pod crashloop-demo
kubectl describe pod crashloop-demo
kubectl logs crashloop-demo
```

The important diagnostic lesson is that `CrashLoopBackOff` is not the root cause. It describes Kubernetes backing off between restart attempts. Logs, termination state and events reveal why the process is exiting.
