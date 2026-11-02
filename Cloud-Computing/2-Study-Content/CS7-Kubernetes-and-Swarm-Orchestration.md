# CS7: Container Orchestration - Kubernetes and Docker Swarm

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Ch.10
> - R7: Kubernetes Documentation (kubernetes.io)
> - R8: Docker Swarm Documentation (docs.docker.com/engine/swarm)
> - ZG527_Kubernetes_Lab_Manual.pdf

---

## Table of Contents

- [7.1 Why Container Orchestration?](#71-why-container-orchestration)
- [7.2 Docker Swarm](#72-docker-swarm)
- [7.3 Kubernetes Architecture](#73-kubernetes-architecture)
- [7.4 Kubernetes Core Concepts](#74-kubernetes-core-concepts)
- [7.5 Kubernetes Deployments and Services](#75-kubernetes-deployments-and-services)
- [7.6 Kubernetes Networking and Storage](#76-kubernetes-networking-and-storage)
- [7.7 Kubernetes Scaling and Self-Healing](#77-kubernetes-scaling-and-self-healing)
- [7.8 Kubernetes vs. Docker Swarm](#78-kubernetes-vs-docker-swarm)

---

## 7.1 Why Container Orchestration?

### The Problem at Scale

When running dozens or hundreds of containers across multiple machines:
- **Manual deployment:** Which container runs on which server? Infeasible at scale
- **High availability:** If container crashes, who restarts it? Where?
- **Load balancing:** Distribute traffic across container replicas
- **Scaling:** Add more containers when demand increases
- **Updates:** Roll out new versions without downtime
- **Networking:** Container-to-container communication across machines
- **Storage:** Persistent data for stateful containers

Container orchestration platforms solve these problems automatically.

### What is Container Orchestration?

Automated management of containerized applications across clusters of machines. Handles:
1. **Scheduling:** Place containers on optimal nodes
2. **Scaling:** Add/remove containers based on demand
3. **Networking:** Enable communication between containers
4. **Storage:** Mount persistent volumes
5. **Self-healing:** Restart failed containers
6. **Rolling updates:** Deploy new versions without downtime
7. **Load balancing:** Distribute traffic

---

## 7.2 Docker Swarm

### Swarm Mode Basics

Docker Swarm is Docker's built-in orchestration platform. Simple alternative to Kubernetes.

**Architecture:**
- **Manager Nodes:** Accept commands, manage cluster state (Raft consensus)
- **Worker Nodes:** Run containers
- **Service:** Collection of container replicas
- **Task:** Individual container instance

### Swarm Commands

```bash
# Initialize swarm
docker swarm init

# Add worker node
docker swarm join --token <WORKER-TOKEN> <MANAGER-IP>:2377

# Deploy service (10 replicas)
docker service create --replicas 10 --name web nginx

# Scale service to 5 replicas
docker service scale web=5

# Update service (rolling update)
docker service update --image nginx:latest web

# View service status
docker service ls
docker service ps web
```

### Swarm vs. Kubernetes

| Aspect | Swarm | Kubernetes |
|--------|-------|-----------|
| **Simplicity** | Very simple; integrated with Docker | Complex; steep learning curve |
| **Setup** | Minutes | Hours/days for production |
| **Networking** | Overlay networks (simple) | Sophisticated networking |
| **Scaling** | Good | Excellent |
| **Ecosystem** | Limited | Extensive (Helm, Istio, etc.) |
| **Production** | Small-medium deployments | Large-scale, complex deployments |

---

## 7.3 Kubernetes Architecture

### Cluster Components

**Master (Control Plane):**
- **API Server:** REST API for cluster management
- **etcd:** Distributed key-value store; cluster state
- **Scheduler:** Assigns pods to nodes
- **Controller Manager:** Ensures desired state

**Nodes (Workers):**
- **kubelet:** Agent on each node; manages pods
- **Container Runtime:** Docker or containerd
- **kube-proxy:** Networking; load balancing

```
                    Master (Control Plane)
                  ┌─────────────────────────┐
                  │   API Server            │
                  │   Scheduler             │
                  │   Controller Manager    │
                  │   etcd (State)          │
                  └────┬────────────────┬───┘
                       │                │
                    ┌──▼──┐          ┌──▼──┐
                    │Node1 │          │Node2 │
                    │kubelet          │kubelet
                    │Pod1, Pod2       │Pod3, Pod4
                    └─────┘          └─────┘
```

### Kubernetes Resources

**Pod:** Smallest deployable unit; one or more containers
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

**Service:** Exposes pods to network; load balancing
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
```

**Deployment:** Manages pod replicas; rolling updates
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

---

## 7.4 Kubernetes Core Concepts

### Pods

- **Smallest unit:** One or more tightly coupled containers
- **Shared network:** Containers in pod share IP address
- **Ephemeral:** Pods are temporary; created/destroyed dynamically
- **Usually created by Deployments:** Don't create pods manually

### Labels and Selectors

**Labels:** Key-value pairs for organization
```yaml
labels:
  app: web
  version: v1
  tier: frontend
```

**Selectors:** Query pods by labels
```yaml
selector:
  app: web
  tier: frontend
```

### Namespaces

Logical cluster partitioning; multi-tenancy
```bash
# Create namespace
kubectl create namespace production

# Deploy to specific namespace
kubectl deploy --namespace production

# List resources in namespace
kubectl get pods --namespace production
```

### ConfigMaps and Secrets

**ConfigMap:** Non-sensitive configuration
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_host: "db.example.com"
  database_port: "5432"
```

**Secret:** Sensitive data (encrypted)
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  username: dXNlcm5hbWU=  # base64 encoded
  password: cGFzc3dvcmQ=  # base64 encoded
```

---

## 7.5 Kubernetes Deployments and Services

### Deployments

Desired state management; ensures replicas running

```bash
# Create deployment
kubectl create deployment web --image=nginx --replicas=3

# View deployments
kubectl get deployments
kubectl describe deployment web

# Update image (rolling update)
kubectl set image deployment/web nginx=nginx:1.19

# Rollback to previous version
kubectl rollout undo deployment/web
```

**Rolling Update Process:**
1. Create new replica with new image
2. Wait for health check to pass
3. Route traffic to new replica
4. Terminate old replica
5. Repeat until all replicas updated
6. No downtime; traffic continuously served

### Services

Expose pods to network; stable IP/DNS

**Service Types:**

1. **ClusterIP (default):** Internal cluster communication
   - Accessible only within cluster
   - Use for internal services

2. **NodePort:** Exposes on node IP:port
   - Accessible from outside cluster
   - Port range: 30000-32767

3. **LoadBalancer:** External load balancer (cloud provider)
   - AWS: ELB, Azure: Azure LB, GCP: Cloud Load Balancer
   - Distributes traffic to service

4. **ExternalName:** DNS CNAME mapping
   - Point to external service

---

## 7.6 Kubernetes Networking and Storage

### Networking

**Pod-to-Pod Communication:**
- Pods on same node: Direct communication via docker bridge
- Pods on different nodes: CNI (Container Network Interface) plugin handles
- Plugins: Flannel, Calico, Weave

**Service Discovery:**
- Kubernetes DNS automatically creates entries for services
- Pod can reach service via DNS: `web-service.default.svc.cluster.local`

### Storage

**Volumes:** Storage mounted in pods

**Types:**
1. **emptyDir:** Temporary; deleted when pod terminated
2. **hostPath:** Mount host directory (node-specific)
3. **PersistentVolume (PV):** Cluster-wide storage
4. **PersistentVolumeClaim (PVC):** Pod requests storage

**Persistent Storage Example:**
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-data
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

---
apiVersion: v1
kind: Pod
metadata:
  name: app-with-storage
spec:
  containers:
  - name: app
    image: myapp
    volumeMounts:
    - mountPath: /data
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: pvc-data
```

---

## 7.7 Kubernetes Scaling and Self-Healing

### Horizontal Pod Autoscaling (HPA)

Automatically scale replicas based on metrics

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

**Behavior:**
- CPU > 70%: Scale up (add replicas)
- CPU < 70%: Scale down (remove replicas)
- Min 2, max 10 replicas

### Self-Healing

**ReplicaSet:** Ensures desired number of replicas

If pod crashes:
1. ReplicaSet detects missing pod
2. Creates replacement pod
3. Scheduler assigns pod to healthy node
4. Service continues without manual intervention

**Liveness Probe:** Restart unhealthy pod

```yaml
spec:
  containers:
  - name: app
    image: myapp
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 30
      periodSeconds: 10
```

If `/health` endpoint returns failure: pod is restarted

### StatefulSets

For stateful applications (databases, message queues)

- **Stable identity:** Pods have predictable names (web-0, web-1, web-2)
- **Persistent storage:** Each pod has dedicated storage
- **Ordered deployment:** Deploy/scale sequentially
- **Ordered termination:** Terminate in reverse order

---

## 7.8 Kubernetes vs. Docker Swarm

### Comparison

| Feature | Kubernetes | Docker Swarm |
|---------|-----------|--------------|
| **Learning Curve** | Steep | Easy |
| **Setup Time** | Hours | Minutes |
| **Scalability** | Excellent (1000s nodes) | Good (100s nodes) |
| **Networking** | Advanced (CNI plugins) | Simple (overlay networks) |
| **Storage** | Sophisticated (PV/PVC) | Basic |
| **Auto-scaling** | Yes (HPA, VPA) | Manual scaling |
| **Load Balancing** | Advanced (Ingress) | Basic (Service) |
| **Community** | Huge; industry standard | Smaller |
| **Production Ready** | Yes; enterprise standard | Yes; simpler deployments |
| **Ecosystem** | Extensive (Helm, Istio, etc.) | Limited |

### Decision Matrix

**Use Kubernetes:**
- Large-scale deployments (100+ containers)
- Complex microservices
- Multi-region deployments
- Production systems
- Need advanced networking/storage
- Team has Kubernetes expertise

**Use Docker Swarm:**
- Simple deployments (< 50 containers)
- Quick prototype/MVP
- Team unfamiliar with orchestration
- All-in-one Docker ecosystem
- Resource-constrained environments

---

## Summary

Container orchestration is essential for managing containerized applications at scale. **Kubernetes** provides sophisticated orchestration suitable for enterprise systems, while **Docker Swarm** offers simplicity for smaller deployments. Both solve similar problems but with different trade-offs between complexity and capability.

