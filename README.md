# Secure Development Operations Assignment – MERN App Due 9/10/2025

A full-stack [MERN](https://www.mongodb.com/mern-stack) application, originally designed to manage employee information, now adapted for the **2808ICT Secure Development Operations** assignment.  
This repository demonstrates containerisation, DevOps security practices, and deployment on cloud infrastructure.

---

## Team Members
- Jenjira Kongpong – s5393441  
- Wen-Dian Lin (Stanley) – s5201143  
- Niyonkuru Justus Ndizeye – s5341234  
- Anthony Le – s5260399  

Project Number: **2 (MERN App)**  
Repository: **doananhtingithub40102/mern-app**

---

## Tech Stack
**Client:** React, Bootstrap  
**Server:** Node.js, Express.js  
**Database:** MongoDB  
**DevOps:** Docker, Docker Compose, Nginx (HTTPS), Kubernetes (Minikube), OWASP ZAP  

---

## Prerequisites
```
# Update system
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg lsb-release openssl

# add Docker’s GPG key and repo
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# enable + start docker
sudo systemctl enable --now docker

# (optional) let current user run docker without sudo
sudo usermod -aG docker $USER
newgrp docker  # start a new shell so group add takes effect

# verify versions
docker --version
docker compose version

# Install Minikube + kubectl
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install kubectl /usr/local/bin/kubectl

# verify versions
minikube version
kubectl version --client
```

## Inbound Rules
### Docker Compose with Nginx
TCP 80 (HTTP)
TCP 443 (HTTPS)
TCP 8081 (Mongo Express)
### Kubernetes — kubectl port-forward
TCP 8080 (maps to service port 80)
TCP 8443 (maps to service port 443)

---

## Milestone Checklist
Progress through the assignment is tracked via the following milestones (✅ = completed, ⬜ = pending):

- [✅] **M1** – Clone project, set up local dev environment, verify app runs (25/8/2025)  
- [✅] **M2** – Create Dockerfiles for frontend & backend (30/8/2025)  
- [✅] **M3** – Configure MongoDB & Mongo Express containers (2/9/2025)  
- [✅] **M4** – Write `docker-compose.yml` for 4-container setup (5/9/2025)  
- [✅] **M5** – Add Nginx reverse proxy with HTTPS (8/9/2025)  
- [✅] **M6** – Test docker-compose integration (logs, data add/update) (10/9/2025)  
- [✅] **M7** – Create Kubernetes manifests (Deployment, Service, Ingress) (4/9/2025)  
- [✅] **M8** – Deploy on Minikube, verify HTTPS & service connectivity (4/9/2025)  
- [✅] **M9** – Perform OWASP ZAP automated scan (25/9/2025)  
- [✅] **M10** – Perform manual security testing (28/9/2025)  
- [✅] **M11** – Analyse 4 vulnerabilities (different CWE codes) (3/10/2025)  
- [✅] **M12** – Implement fixes & document remediation steps (6/10/2025)  
- [✅] **M13** – Write Task 2 & 3 reports (PDF) (8/10/2025)  
- [✅] **M14** – Record 5-min individual video statements (9/10/2025)  

---

## Running Using Docker

### 1. Clone the project
```
git clone https://github.com/yellowthing0/mern-app.git
cd mern-app
```

### 2. Re-generate certs
```
mkdir -p nginx/certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout nginx/certs/selfsigned.key \
  -out nginx/certs/selfsigned.crt \
  -subj "/CN=localhost"
```

### 3. compose first time
```
sudo docker compose up -d --build
```

## Running Using Minikube

### 1. Start Minikube
```
minikube start --driver=docker --disk-size=20g
```

### 2. Point Docker to Minikube
```
eval $(minikube docker-env)

docker build -t mern-backend:dev -f server/Dockerfile server
docker build -t mern-frontend:dev -f client/Dockerfile client
```
### 3. Create TLS secret for Nginx
```
kubectl create secret generic nginx-certs \
  --from-file=nginx/certs/selfsigned.crt \
  --from-file=nginx/certs/selfsigned.key
```

### 4. Apply Kubernetes manifests
```
kubectl apply -f kubernetes/
```

### 4. Portforward Public ip
```
kubectl port-forward --address 0.0.0.0 svc/nginx-service 8080:80 8443:443
```

### Testing
```
sudo docker ps

# Minikube status
minikube status

# Cluster info
kubectl get nodes
kubectl get pods
kubectl get svc


curl -I  http://<EC2_PUBLIC_IP> 
curl -Ik https://<EC2_PUBLIC_IP>:8443
```
