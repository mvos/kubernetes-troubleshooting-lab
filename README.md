# Kubernetes Troubleshooting Lab

Hands-on Kubernetes troubleshooting scenarios designed to demonstrate practical skills expected from **Kubernetes Support Engineers, SREs, Platform Engineers, and DevOps Engineers**.

The goal of this repository is not just to deploy workloads that work. Each lab intentionally introduces a realistic failure so you can practice the investigation workflow used in production environments: observe symptoms, collect evidence, identify the root cause, apply a fix, and verify recovery.

## Skills demonstrated

- Kubernetes workload troubleshooting
- `kubectl` diagnostics
- Pod lifecycle and container failures
- Resource requests and limits
- Services and label selectors
- DNS and service discovery
- PersistentVolumeClaims
- Scheduling constraints
- Node troubleshooting
- Readiness and liveness probes
- Incident-style root cause analysis

## Requirements

- Kubernetes cluster (kind, minikube, Docker Desktop, AKS, EKS or GKE)
- `kubectl`

Check access before starting:

```bash
kubectl cluster-info
kubectl get nodes
```

## Labs

| # | Scenario | Primary symptom | Main concepts |
|---|---|---|---|
| 01 | [CrashLoopBackOff](scenarios/01-crashloopbackoff/) | Container repeatedly restarts | logs, describe, exit codes |
| 02 | [ImagePullBackOff](scenarios/02-imagepullbackoff/) | Image cannot be pulled | events, image names, registry |
| 03 | [OOMKilled](scenarios/03-oomkilled/) | Container killed by memory limit | resources, metrics, exit 137 |
| 04 | [Pending Pod](scenarios/04-pending-pod/) | Pod never gets scheduled | requests, scheduler events |
| 05 | [Service Connectivity](scenarios/05-service-connectivity/) | Service has no working backend | selectors, endpoints, labels |
| 06 | [DNS Failure](scenarios/06-dns-failure/) | Service name cannot be resolved | CoreDNS, namespaces, FQDN |
| 07 | [PVC Pending](scenarios/07-pvc-pending/) | PVC remains Pending | StorageClass, provisioning |
| 08 | [NodeNotReady](scenarios/08-node-notready/) | Workloads affected by node health | nodes, conditions, kubelet |

## Troubleshooting workflow

A useful production workflow is:

```text
1. Identify the symptom
        ↓
2. Check resource status
        ↓
3. Inspect events
        ↓
4. Inspect logs / previous logs
        ↓
5. Validate configuration and dependencies
        ↓
6. Form a hypothesis
        ↓
7. Apply the smallest safe fix
        ↓
8. Verify recovery
```

Common commands:

```bash
kubectl get pods -A
kubectl get events -A --sort-by=.lastTimestamp
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl get pod <pod> -o yaml
kubectl get svc,endpoints -A
kubectl top pods
kubectl top nodes
```

## How to use the repository

Each scenario contains a deliberately broken manifest and a solution guide.

Apply the broken workload first:

```bash
kubectl apply -f scenarios/01-crashloopbackoff/broken.yaml
```

Investigate without opening `solution.md`. After identifying the root cause, compare your diagnosis with the documented solution.

Cleanup:

```bash
kubectl delete -f scenarios/01-crashloopbackoff/broken.yaml
```

## Repository structure

```text
kubernetes-troubleshooting-lab/
├── README.md
├── docs/
│   └── troubleshooting-cheatsheet.md
└── scenarios/
    ├── 01-crashloopbackoff/
    ├── 02-imagepullbackoff/
    ├── 03-oomkilled/
    ├── 04-pending-pod/
    ├── 05-service-connectivity/
    ├── 06-dns-failure/
    ├── 07-pvc-pending/
    └── 08-node-notready/
```

## Important note

Some symptoms vary slightly between Kubernetes distributions and versions. The investigation method is more important than memorizing a particular error string.

## Author

**Marcus Vinicius Oliveira Silva**

Infrastructure / Middleware / SRE professional focused on Kubernetes, Linux, Kafka, Redis, Debezium, cloud infrastructure and production troubleshooting.

---

If you are reviewing this repository as part of a technical interview, start with the scenarios directory. Each lab is designed around a concrete failure and an evidence-driven troubleshooting process.
