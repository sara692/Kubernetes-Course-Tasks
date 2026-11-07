# Task 1: Multi-container Pods (Init Container & Sidecar)

## What I did
 
1. Launched an EC2 instance on AWS and connected to it over SSH with a key pair.
2. Added swap, installed Docker, kubectl and minikube.
3. Created the cluster:
```bash
   minikube start --nodes 3 --cni=calico --cpus 2 --memory 2200
   kubectl get nodes
```
4. Created the namespace and the Deployment:
```bash
   kubectl apply -f namespace.yaml
   kubectl apply -f deployment.yaml
   kubectl get pods -n webapp -w
```
5. Checked that the page works and the time changes every 5 seconds.
6. Changed only the init container command to `exit 1`, applied it, and observed the result.
7. Restored the working init command and confirmed the Deployment is `3/3` again.

## Screenshots
### Cluster
<img width="596" height="383" alt="image" src="https://github.com/user-attachments/assets/147d7459-f72b-4b4f-858f-3be75ef6d10e" />

### Deployment and pods
<img width="638" height="186" alt="Capture1" src="https://github.com/user-attachments/assets/0325ea60-654e-476f-97a6-45572842c1f1" />

### Page updating
<img width="1091" height="182" alt="Capture2" src="https://github.com/user-attachments/assets/45609699-6d44-43d7-8878-101f8473ac44" />

### Verification checks
<img width="1896" height="647" alt="Capture3" src="https://github.com/user-attachments/assets/4a38c361-80dd-45e7-bce7-2da3841cf2be" />

### Failing init container (Step 5)
<img width="842" height="299" alt="Capture4" src="https://github.com/user-attachments/assets/086f08ab-1d7e-4475-b54a-631ebc837544" />

### Fixed (Step 6)
<img width="1109" height="324" alt="Capture5" src="https://github.com/user-attachments/assets/f0810a30-c478-4291-b1ba-82ac5fb7e867" />

## Answers
- **Status of the new Pod:** Init:Error, then Init:CrashLoopBackOff (READY 0/2).
- **Do nginx and time-updater start?** No. Main containers start only after all init containers succeed.
- **Is the page still reachable?** Yes. The rolling update keeps the 3 old healthy pods until the new one is Ready, so traffic continues to be served.

## Implementer Details
 
| Field            | Details                                      |
|------------------|----------------------------------------------|
| Name             | Sara Ibrahim Mohamed Omran                           |
| GitHub           | sara692                     |
| Email            | saraomran433@gmail.com                               |
| Course           | Kubernetes Course - MLOps MENA Community     |
| Task             | Task Session 1                               |
| Date             | 2026-11-07                                   |
 
