# Task 2: FastAPI + MySQL with ConfigMap, Secret & Ingress

## Implementer Details

| Field  | Details                                  |
| ------ | ---------------------------------------- |
| Name   | `Sara Ibrahim Mohamed Omran`             |
| GitHub | `sara692`                                |
| Email  | `saraomran433@gmail.com`                 |
| Course | Kubernetes Course - MLOps MENA Community |
| Task   | Task Session 2                           |
| Date   | 2026-11-17                               |

## Environment

| Item       | Value                                       |
| ---------- | ------------------------------------------- |
| Cloud      | AWS EC2 (us-east-1), m7i-flex.large, Ubuntu |
| Kubernetes | minikube (Docker driver), 3 nodes, Calico   |
| Ingress    | NGINX Ingress Controller (minikube addon)   |

## Description

A FastAPI guestbook application backed by one MySQL server on Kubernetes:

- MySQL keeps its data on a PV + PVC (`mysql-pv` / `mysql-pvc`, 2Gi, `hostPath`, storageClass `manual`),
  pinned to the worker `minikube-m02` with node affinity.
- App settings are in the ConfigMap `webapp-config`. MySQL credentials are in the Secret `mysql-secret`.
- Users reach the app through the Ingress `webapp-ingress` (path `/`) to the Service `webapp-api` (80 to 8000), 2 app Pods.

```
Browser -> Ingress Controller -> Ingress webapp-ingress -> Service webapp-api (80 -> 8000) -> 2 app Pods
                                                                                  |
                                                                       Service mysql (3306) -> MySQL Pod -> mysql-pvc
```

## Files

| File             | Content                                             |
| ---------------- | --------------------------------------------------- |
| `namespace.yaml` | Namespace `webapp-stack`                            |
| `secret.yaml`    | Secret `mysql-secret`                               |
| `configmap.yaml` | ConfigMap `webapp-config`                           |
| `pv-pvc.yaml`    | PV `mysql-pv` (with nodeAffinity) + PVC `mysql-pvc` |
| `mysql.yaml`     | MySQL Deployment + Service                          |
| `webapp.yaml`    | `webapp-api` Deployment + Service                   |
| `ingress.yaml`   | Ingress `webapp-ingress`                            |

## What I did

1. Cloned the course repo, built `webapp-api:task2` and `webapp-mysql:task2`, and loaded them into minikube.
2. Created the namespace, Secret, ConfigMap, PV and PVC, MySQL, the app, and the Ingress, in that order.
3. Opened the app through the Ingress controller and tested `/api/info` and `/api/messages`.
4. Deleted the MySQL Pod and checked that the data survived.

## Screenshots

### 1. Images loaded into minikube

![alt text](Capture6.PNG)

### 2. Secret and ConfigMap

![alt text](Capture7.PNG)

### 3. PV and PVC bound

![alt text](Capture8.PNG)

### 4. Deployments ready and MySQL on minikube-m02

![alt text](image-3.png)

### 5. App environment variables

![alt text](Capture9.PNG)

### 6. Default data in MySQL

![alt text](image-1.png)

### 7. Ingress

![alt text](Capture10.PNG)

### 8. Ingress access: `/api/info`

![alt text](Capture11.PNG)

### 9. Writing and reading messages: `/api/messages`

![alt text](image-2.png)

### 10. Data persistence after deleting the MySQL Pod

![alt text](image.png)

## Notes

- **Why nodeAffinity on the PV:** a `hostPath` folder exists on one node only. Without nodeAffinity, the MySQL Pod could start on another node after a restart, get a new empty folder, and lose the data. With nodeAffinity every Pod using this PV is scheduled on `minikube-m02`, where the data is.
- **Why `strategy: Recreate` for MySQL:** the old Pod stops before the new one starts, so two MySQL servers never use the same data folder.
- **Why `MYSQL_PORT` is in the ConfigMap:** Kubernetes adds `MYSQL_PORT=tcp://<ip>:3306` for the Service `mysql`. The ConfigMap value (`3306`) replaces it so the app can use it.
- **Database creation happens only on the first start** (empty data folder), so changing the Secret later does not change MySQL's passwords.

## Result

| #   | Check               | Result                                               |
| --- | ------------------- | ---------------------------------------------------- |
| 1   | Images loaded       | `webapp-api:task2`, `webapp-mysql:task2`             |
| 2   | Secret / ConfigMap  | DATA 3 / 5                                           |
| 3   | PV and PVC          | Bound, 2Gi                                           |
| 4   | Deployments         | mysql 1/1, webapp-api 2/2                            |
| 5   | MySQL node          | minikube-m02                                         |
| 6   | Ingress             | CLASS nginx, ADDRESS filled                          |
| 7   | `/api/info`         | version task2, title "hello in MLOPS MENA COMMUNITY" |
| 8   | New message written | id 3                                                 |
| 9   | Persistence         | message still there after the MySQL Pod was deleted  |

