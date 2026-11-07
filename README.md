# Kubernetes Course Tasks

Hands-on tasks for the **Kubernetes Course** by the MLOps MENA Community.
Each task has its own folder with the manifests, a README, and screenshots of the commands and results.

## Implementer Details

| Field    | Details                                  |
|----------|------------------------------------------|
| Name     | Sara Ibrahim Mohamed Omran                       |
| GitHub   | sara692                 |
| Email    | saraomran433@gmail.com                           |
| Course   | Kubernetes Course - MLOps MENA Community |

## Lab Environment

| Item           | Value                                         |
|----------------|-----------------------------------------------|
| Cloud          | AWS EC2 (us-east-1)                           |
| Instance       | m7i-flex.large (2 vCPU, 8 GB RAM), Ubuntu LTS |
| Kubernetes     | minikube (Docker driver), 3 nodes             |
| Network plugin | Calico                                        |
| Tools          | Docker, kubectl, minikube                     |

Cluster creation command used for all tasks:

```bash
minikube start --nodes 3 --cni=calico --cpus 2 --memory 2200
```

## Tasks

| Task | Topic | Folder | Status |
|------|-------|--------|--------|
| 1 | Multi-container Pods (Init Container & Sidecar) | [Task-1](./Task-1) | Done |
| 2 | _to be added_ | Task-2 | Pending |
| 3 | _to be added_ | Task-3 | Pending |
| 4 | _to be added_ | Task-3 | Pending |
| 5 | _to be added_ | Task-3 | Pending |

## Task 1 Summary

Ran an nginx "hello" page on a 3-node minikube cluster. Each Pod has:

- `init-done`: an init container that prints `done` and exits
- `nginx`: the main container that writes and serves `index.html`
- `time-updater`: a sidecar that rewrites the page with the new time every 5 seconds

The Pod uses `shareProcessNamespace: true` and no volumes. Making the init container fail showed that
the new Pod stays in `Init:CrashLoopBackOff` and never starts its main containers, while the old Pods
keep serving traffic during the rolling update.

See [Task-1/README.md](./Task-1/README.md) for the steps, screenshots and answers.

## Repository Structure

```
Kubernetes-Course-Tasks/
├── README.md
├── Task-1/
│   ├── README.md
│   ├── namespace.yaml
│   ├── deployment.yaml
├── Task-2/
└── ...
```


- The same cluster is reused across tasks.
- After stopping and starting the EC2 instance, run `sudo swapon /swapfile` and then `minikube start`.
- The EC2 instance is stopped when not in use to save credits.
