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

<img width="784" height="95" alt="image" src="https://github.com/user-attachments/assets/a40153d8-c530-4ad8-8f15-fa2ec3dba7cc" />

### 2. Secret and ConfigMap

<img width="809" height="231" alt="image" src="https://github.com/user-attachments/assets/761b9d15-87f5-4c34-8189-8735c97c0506" />

### 3. PV and PVC bound

<img width="1364" height="267" alt="image" src="https://github.com/user-attachments/assets/8ec2a0e8-6701-4768-9572-636cc1a607c3" />

### 4. Deployments ready and MySQL on minikube-m02

<img width="1329" height="148" alt="image" src="https://github.com/user-attachments/assets/c8dde21b-ad03-4ec0-a48f-9576921dd076" />

### 5. App environment variables

<img width="1220" height="466" alt="image" src="https://github.com/user-attachments/assets/2f0cfb54-c5f3-4786-b1c6-bdc59e74425a" />

### 6. Default data in MySQL

<img width="1516" height="120" alt="image" src="https://github.com/user-attachments/assets/b153faa0-d6ae-40c1-bf70-ee88340a8046" />

### 7. Ingress

<img width="812" height="111" alt="image" src="https://github.com/user-attachments/assets/39afc01b-5a74-4525-8c76-ef1cac9a3246" />

### 8. Ingress access: `/api/info`

<img width="1896" height="116" alt="image" src="https://github.com/user-attachments/assets/17795bca-bc70-4771-8c2b-91684a3873f4" />

### 9. Writing and reading messages: `/api/messages`

<img width="1889" height="97" alt="image" src="https://github.com/user-attachments/assets/93998dcb-5d81-4fb9-916c-25ceacaf11a7" />

### 10. Data persistence after deleting the MySQL Pod

<img width="908" height="145" alt="image" src="https://github.com/user-attachments/assets/10b0399a-4d99-409b-a820-a9d86e8d2d60" />

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

