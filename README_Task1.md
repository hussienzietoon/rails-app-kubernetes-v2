# 🚀 Task 1 - Fullstack App with Nginx, Docker, and Kubernetes

This project demonstrates a simple fullstack setup using:

- 🧠 **React frontend** served via Nginx
- 💎 **Ruby backend** (WEBrick server)
- 📦 Dockerized services
- ☸️ Deployed on **Kubernetes** using `minikube`
- 🔄 Probes for application health
- 🐳 Images hosted on Docker Hub

---

## 📁 Project Structure

```
.
├── frontend/              # React frontend (built and served via Nginx)
│   ├── build/              # React build output
│   ├── nginx.conf          # Nginx config with health and proxy
│   └── Dockerfile
├── webServer/                # Ruby backend API
│   ├── app.rb              # Main Ruby server
│   └── Dockerfile
├── k8s/
│   ├── frontend-deployment.yaml
│   ├── backend-deployment.yaml
│   ├── frontend-service.yaml
│   └── backend-service.yaml
└── README.md
```

---

## 🐳 Docker Images

| Component | Docker Image                            |
| --------- | --------------------------------------- |
| Frontend  | `hussienmohamed/frontend_espace:latest` |
| Backend   | `hussienmohamed/webserver:latest`       |

---

## 🛠️ Build & Push

### Frontend

```bash
docker build -t hussienmohamed/frontend_espace:latest ./frontend
docker push hussienmohamed/frontend_espace:latest
```

### Backend

```bash
docker build -t hussienmohamed/backend_espace:latest ./webServer
docker push hussienmohamed/backend_espace:latest
```

---

## ☸️ Kubernetes Deployment

```bash
kubectl apply -f k8s/
```

> Make sure `minikube` is running.

---

## 🌐 Accessing the App

```bash
minikube service frontend
```

This will open the React app in your browser.

---

## 💚 Health Checks

### 1. **Frontend `/health`**

Handled **directly** by Nginx:

```nginx
location /health {
  return 200 'OK';
  add_header Content-Type text/plain;
}
```

Used by Kubernetes **probes**.

### 2. **Backend `/backend-health`**

Proxied through Nginx:

```nginx
location /backend-health {
  proxy_pass http://backend:8081/health;
}
```

Can be accessed by browser or curl.

---

## 📦 Probes in Kubernetes

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
readinessProbe:
  httpGet:
    path: /health
    port: 80
```

Used to ensure the frontend pod is running properly.

---

---

## ✍️ Author

- **Hussien Mohamed**
- DevOps Intern @ eSpace
- [DockerHub](https://hub.docker.com/u/hussienmohamed)
- [LinkedIn](https://www.linkedin.com/in/hussien-mohamed-zietoon-9960ba317)

---

## 📌 Notes

- `nginx.conf` is customized to serve SPA routing and act as a proxy.
- All paths like `/dashboard`, `/about`, etc., are served using `try_files` with React routing.
- Docker & K8s are used together for real-world deployment simulation.

---
