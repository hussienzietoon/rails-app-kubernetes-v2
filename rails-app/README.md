# Rails and React Application Deployment on Kubernetes

This repository contains a containerized web application with a React frontend and Rails backend, configured for deployment on Kubernetes.

## Architecture

The application consists of two main components:

### Frontend

- React application served by Nginx
- Configured with health checks at `/health`
- Integration check endpoint at `/backend-health`
- Resource limits: 50m-100m CPU, 64Mi-128Mi memory
- Container port: 80

### Backend

- Ruby on Rails application (Webrick server)
- Health check endpoint at `/health`
- Resource limits: 50m-100m CPU, 64Mi-128Mi memory
- Container port: 8081

## Prerequisites

- Kubernetes cluster (Minikube for local development)
- kubectl CLI tool
- Docker (for building images if needed)

## Docker Images

The application uses the following Docker images:

- Frontend: `hussienmohamed/frontend_espace:latest`
- Backend: `hussienmohamed/webserver:latest`

## Deployment Steps

1. Start Minikube:

   ```bash
   minikube start
   ```

2. Add required node taint:

   ```bash
   kubectl taint nodes minikube app=critical:NoSchedule
   ```

3. Deploy the applications:

   ```bash
   # Deploy backend
   kubectl apply -f webserver_deployment.yml
   kubectl apply -f webserver_service.yml

   # Deploy frontend
   kubectl apply -f frontend_deployment.yml
   kubectl apply -f frontend_service.yml
   ```

## Configuration Details

### Node Taints and Tolerations

The deployments are configured to run on tainted nodes with:

```yaml
tolerations:
  - key: "app"
    operator: "Equal"
    value: "critical"
    effect: "NoSchedule"
```

### Health Checks

#### Frontend Health Checks

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 15
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### Backend Health Checks

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8081
  initialDelaySeconds: 15
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /health
    port: 8081
  initialDelaySeconds: 5
  periodSeconds: 5
```

### Resource Management

Both frontend and backend pods are configured with resource limits:

```yaml
resources:
  requests:
    cpu: 50m
    memory: 64Mi
  limits:
    cpu: 100m
    memory: 128Mi
```

## Verification Steps

1. Check pod status:

   ```bash
   kubectl get pods
   ```

2. Verify health checks:

   ```bash
   # Frontend health
   minikube service frontend
   # Note the URL and port provided by minikube
   curl http://<minikube-ip>:<port>/health

   # Backend health
   kubectl port-forward svc/backend-service 8081:8081
   curl http://localhost:8081/health
   ```

3. Check integration:
   ```bash
   minikube service frontend
   # Note the URL and port provided by minikube
   curl http://<minikube-ip>:<port>/backend-health
   ```

## Troubleshooting

1. Check pod logs:

   ```bash
   kubectl logs <pod-name>
   ```

2. Check pod details:

   ```bash
   kubectl describe pod <pod-name>
   ```

3. Common issues:
   - If pods are pending, check node taints and tolerations
   - If health checks fail, ensure services are running correctly
   - For resource issues, check pod resource utilization:
     ```bash
     kubectl top pod <pod-name>
     ```

## Scaling

To scale the deployments:

```bash
kubectl scale deployment frontend-deployment --replicas=3
kubectl scale deployment backend --replicas=3
```
