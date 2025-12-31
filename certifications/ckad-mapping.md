# CKAD (Certified Kubernetes Application Developer) - ZaveStudios Platform Mapping

**Exam:** Certified Kubernetes Application Developer (CKAD) v1.34  
**Platform:** ZaveStudios  
**Purpose:** Map platform implementation to CKAD exam domains for integrated learning and demonstration

## Overview

This document maps ZaveStudios platform components and Kubernetes implementations to CKAD exam objectives. The CKAD certification validates skills in designing, building, configuring, and deploying cloud-native applications on Kubernetes.

**Study Strategy:** ZaveStudios provides hands-on implementation of most CKAD concepts. As you build applications and platform services, you're simultaneously preparing for the exam through practical experience.

---

## CKAD Exam Domains

1. **Application Design and Build** (20%)
2. **Application Deployment** (20%)
3. **Application Observability and Maintenance** (15%)
4. **Application Environment, Configuration and Security** (25%)
5. **Services and Networking** (20%)

---

## Domain 1: Application Design and Build (20%)

### Define, build and modify container images

**Exam Topics:**
- Dockerfile creation
- Multi-stage builds
- Container image optimization
- Image tagging and versioning
- Registry operations

**Platform Implementation:**

#### Container Image Creation
- **Component:** Application Dockerfiles
- **Implementation:**
  - Multi-stage builds for minimal image sizes
  - Optimized layer caching
  - Non-root user containers
  - Distroless base images where appropriate
- **Evidence:**
  - Application repositories (xavierlopez.me, data pipelines, AI services)
  - GitHub Issues: #28 (Deploy xavierlopez.me), #29 (Data pipeline), #30 (AI service)
- **Exam Relevance:** Dockerfile best practices, build optimization

#### Container Registry Usage
- **Component:** AWS ECR integration
- **Implementation:**
  - Automated image builds in GitLab CI/CD
  - Image scanning on push
  - Tag-based versioning strategy
- **Evidence:**
  - GitLab CI/CD pipelines for applications
  - ECR configuration in terraform-modules
- **Exam Relevance:** Registry operations, image lifecycle management

**Key Exam Concepts Demonstrated:**
- ✅ Dockerfile creation and optimization
- ✅ Multi-stage builds
- ✅ Container registry operations
- ✅ Image tagging strategies
- ✅ Build automation

**Hands-On Practice:**
```dockerfile
# Example multi-stage build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
USER node
EXPOSE 3000
CMD ["node", "server.js"]
```

---

### Choose and use the right workload resource

**Exam Topics:**
- Deployments
- StatefulSets
- DaemonSets
- Jobs
- CronJobs
- When to use each workload type

**Platform Implementation:**

#### Deployments (Stateless Applications)
- **Component:** Web applications, API services
- **Implementation:**
  - xavierlopez.me portfolio site (Deployment)
  - AI inference services (Deployment)
  - Stateless microservices
- **Evidence:**
  - Application manifests in app repositories
  - ArgoCD application configurations
  - GitHub Issue: #28 (Deploy xavierlopez.me)
- **Exam Relevance:** Deployment creation, replica management, rolling updates

#### CronJobs (Scheduled Tasks)
- **Component:** Data engineering pipelines
- **Implementation:**
  - Scheduled ETL jobs
  - Periodic data processing
  - Cleanup tasks
- **Evidence:**
  - Data pipeline CronJob manifests
  - GitHub Issue: #29 (Data pipeline deployment)
- **Exam Relevance:** CronJob syntax, scheduling, concurrency policies

#### DaemonSets (Node-level Services)
- **Component:** Platform monitoring and logging
- **Implementation:**
  - Node exporter for Prometheus (deployed by Big Bang)
  - Log collectors
- **Evidence:**
  - Big Bang DaemonSet configurations
  - Monitoring stack deployment
- **Exam Relevance:** DaemonSet use cases, node selectors

**Key Exam Concepts Demonstrated:**
- ✅ Deployment creation and management
- ✅ CronJob scheduling
- ✅ DaemonSet deployment
- ✅ Workload type selection based on use case
- ⚠️ StatefulSets (used by platform databases, but not custom apps yet)

**Study Focus:**
- When to use each workload type
- StatefulSet for databases (understand concept even if not implementing)
- Job vs CronJob differences

---

### Understand multi-container Pod design patterns

**Exam Topics:**
- Sidecar pattern
- Init containers
- Adapter pattern
- Ambassador pattern

**Platform Implementation:**

#### Sidecar Pattern (Istio Service Mesh)
- **Component:** Istio Envoy sidecars
- **Implementation:**
  - Automatic sidecar injection via Istio
  - Every pod gets Envoy proxy sidecar
  - mTLS, traffic management, observability
- **Design Decision:** Service mesh architecture
- **Evidence:**
  - Big Bang Istio configuration
  - Pods with istio-proxy sidecar containers
- **Exam Relevance:** Sidecar pattern, pod-level proxies, multi-container communication

#### Init Containers
- **Component:** Application initialization
- **Implementation:**
  - Database schema migration init containers
  - Configuration fetching before app start
  - Dependency waiting (wait-for-db patterns)
- **Evidence:**
  - Application deployment manifests
  - Init container configurations
- **Exam Relevance:** Init container syntax, execution order, use cases

#### Logging Sidecar (Future Implementation)
- **Component:** Log aggregation sidecar
- **Implementation:** Potential future pattern for log shipping
- **Exam Relevance:** Sidecar for auxiliary functionality

**Key Exam Concepts Demonstrated:**
- ✅ Sidecar pattern (extensive use via Istio)
- ✅ Init containers for setup tasks
- ✅ Multi-container pod communication
- ✅ Shared volumes between containers
- ⚠️ Adapter and Ambassador patterns (understand conceptually)

**Hands-On Practice:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-example
spec:
  initContainers:
  - name: init-db
    image: busybox
    command: ['sh', '-c', 'until nc -z db 5432; do sleep 1; done']
  containers:
  - name: app
    image: myapp:latest
    ports:
    - containerPort: 8080
  - name: log-shipper
    image: fluentd:latest
    volumeMounts:
    - name: logs
      mountPath: /var/log
  volumes:
  - name: logs
    emptyDir: {}
```

---

### Utilize persistent and ephemeral volumes

**Exam Topics:**
- PersistentVolumeClaims
- PersistentVolumes
- StorageClasses
- Volume types (emptyDir, hostPath, etc.)
- Volume mounts

**Platform Implementation:**

#### StorageClass Configuration
- **Component:** EBS-backed storage for EKS
- **Implementation:**
  - gp3 StorageClass (default)
  - Dynamic volume provisioning
  - Volume expansion enabled
- **Design Decision:** AWS EBS for persistent storage
- **Evidence:**
  - Storage class configurations in EKS setup
  - terraform-modules/eks
- **Exam Relevance:** StorageClass creation, default storage class

#### Persistent Volumes for Stateful Workloads
- **Component:** Database persistence, GitLab storage
- **Implementation:**
  - GitLab uses PVCs for repositories and uploads
  - Prometheus uses PVCs for metrics retention
  - Future: PostgreSQL with persistent storage
- **Evidence:**
  - Big Bang package PVC configurations
  - Platform service storage requirements
- **Exam Relevance:** PVC creation, storage requests, access modes

#### Ephemeral Volumes
- **Component:** Temporary storage for pods
- **Implementation:**
  - emptyDir for shared data between containers
  - Scratch space for processing
  - Cache directories
- **Evidence:**
  - Application pod specifications
  - Init container shared volumes
- **Exam Relevance:** emptyDir, volume lifecycle

**Key Exam Concepts Demonstrated:**
- ✅ PersistentVolumeClaim creation and usage
- ✅ StorageClass configuration
- ✅ emptyDir for ephemeral storage
- ✅ Volume mounts in pod specs
- ✅ Access modes (ReadWriteOnce, ReadWriteMany)
- ⚠️ Static PV provisioning (mostly using dynamic provisioning)

**Hands-On Practice:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: gp3
  resources:
    requests:
      storage: 10Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: app-data
```

---

## Domain 2: Application Deployment (20%)

### Use Kubernetes primitives to implement common deployment strategies

**Exam Topics:**
- Blue/green deployments
- Canary deployments
- Rolling updates
- Rollbacks

**Platform Implementation:**

#### Rolling Updates (Default Strategy)
- **Component:** Deployment rolling update strategy
- **Implementation:**
  - Default Deployment strategy for all applications
  - Controlled rollout with maxUnavailable and maxSurge
  - Health checks ensure zero-downtime
- **Evidence:**
  - Application deployment manifests
  - ArgoCD sync policies
- **Exam Relevance:** Rolling update configuration, zero-downtime deployments

#### Blue/Green via ArgoCD
- **Component:** ArgoCD sync waves and hooks
- **Implementation:**
  - Deploy new version (green) alongside old (blue)
  - Switch traffic via Service selector update
  - Rollback by reverting Service
- **Design Decision:** Safe deployment pattern for critical apps
- **Evidence:**
  - ArgoCD application configurations
  - Service selector strategies
- **Exam Relevance:** Blue/green pattern implementation, service routing

#### Canary Deployments (Future with Istio)
- **Component:** Istio traffic splitting
- **Implementation:**
  - VirtualService for percentage-based routing
  - Gradually increase traffic to new version
  - Monitor metrics before full rollout
- **Evidence:**
  - Istio VirtualService configurations (future)
- **Exam Relevance:** Traffic management, gradual rollouts

**Key Exam Concepts Demonstrated:**
- ✅ Rolling updates
- ✅ Deployment strategies
- ✅ Blue/green pattern
- ✅ Zero-downtime deployments
- ⚠️ Canary (understand via Istio capabilities, may not implement initially)

**Hands-On Practice:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  template:
    spec:
      containers:
      - name: app
        image: myapp:v2
        readinessProbe:
          httpGet:
            path: /health
            port: 8080
```

---

### Understand Deployments and how to perform rolling updates

**Exam Topics:**
- Deployment creation
- Update strategies
- Rollout status
- Rollback operations
- Revision history

**Platform Implementation:**

#### Deployment Management
- **Component:** All stateless applications
- **Implementation:**
  - Declarative Deployment manifests
  - GitOps-driven updates (ArgoCD)
  - Automated rollouts on image updates
- **Evidence:**
  - Application deployment YAML files
  - ArgoCD application definitions
  - GitHub Issues: #28, #29, #30
- **Exam Relevance:** kubectl rollout commands, deployment status

#### Rollback Procedures
- **Component:** Version control and GitOps
- **Implementation:**
  - Git revert for declarative rollback
  - ArgoCD rollback to previous sync
  - Deployment revision history
- **Evidence:**
  - Git commit history
  - ArgoCD sync history
- **Exam Relevance:** kubectl rollout undo, revision management

**Key Exam Concepts Demonstrated:**
- ✅ Deployment creation and updates
- ✅ Rolling update mechanics
- ✅ Rollout status monitoring
- ✅ Rollback operations
- ✅ Revision history management

**Essential kubectl Commands:**
```bash
# Check rollout status
kubectl rollout status deployment/app

# View rollout history
kubectl rollout history deployment/app

# Rollback to previous version
kubectl rollout undo deployment/app

# Rollback to specific revision
kubectl rollout undo deployment/app --to-revision=2

# Pause/resume rollout
kubectl rollout pause deployment/app
kubectl rollout resume deployment/app
```

---

### Use the Helm package manager to deploy existing packages

**Exam Topics:**
- Helm chart installation
- Values customization
- Helm upgrades
- Helm rollbacks
- Chart repositories

**Platform Implementation:**

#### Big Bang (Helm Chart of Charts)
- **Component:** Platform foundation via Helm
- **Implementation:**
  - Big Bang is a meta Helm chart
  - Deploys multiple sub-charts (GitLab, ArgoCD, Istio, etc.)
  - Values-based configuration
  - Helm release management via Flux
- **Evidence:**
  - Big Bang helm chart deployment
  - values.yaml customizations
  - GitHub Issue: #18 (Fork and configure Big Bang)
- **Exam Relevance:** Complex Helm deployments, values hierarchy

#### Helm for Application Deployment
- **Component:** Applications packaged as Helm charts
- **Implementation:**
  - Create Helm charts for applications
  - Deploy via ArgoCD (which understands Helm)
  - Manage releases across environments
- **Evidence:**
  - Application Helm charts
  - ArgoCD Application resources pointing to Helm charts
- **Exam Relevance:** Helm chart creation, templating, release management

**Key Exam Concepts Demonstrated:**
- ✅ Helm chart installation
- ✅ Values customization
- ✅ Helm upgrades and rollbacks
- ✅ Repository management
- ✅ Chart templating (values.yaml, templates/)

**Essential Helm Commands:**
```bash
# Install chart
helm install myapp ./mychart -f values.yaml

# Upgrade release
helm upgrade myapp ./mychart -f values.yaml

# Rollback release
helm rollback myapp 1

# List releases
helm list

# Get values
helm get values myapp

# Template rendering (dry-run)
helm template myapp ./mychart -f values.yaml
```

---

### Kustomize

**Exam Topics:**
- Kustomization files
- Base and overlays
- Patching resources
- Resource composition

**Platform Implementation:**

#### Flux with Kustomize
- **Component:** Flux Kustomization resources
- **Implementation:**
  - Flux uses Kustomize for manifest composition
  - Big Bang deployment uses kustomization.yaml
  - Environment-specific overlays (dev, staging, prod)
- **Evidence:**
  - Flux Kustomization CRs
  - Big Bang kustomization files
  - GitHub Issue: #19 (Configure Flux to deploy Big Bang)
- **Exam Relevance:** Kustomization syntax, overlay pattern

#### Application Kustomize Overlays
- **Component:** Environment-specific configurations
- **Implementation:**
  - Base manifests shared across environments
  - Overlays for dev, staging, prod differences
  - Image tag updates via kustomization
- **Evidence:**
  - Application repository structure
  - ArgoCD applications pointing to kustomize directories
- **Exam Relevance:** Base/overlay pattern, strategic merge patches

**Key Exam Concepts Demonstrated:**
- ✅ Kustomization file structure
- ✅ Base and overlay pattern
- ✅ Resource patching
- ✅ Image tag updates
- ✅ ConfigMap and Secret generation

**Kustomize Example:**
```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namespace: production

images:
  - name: myapp
    newTag: v1.2.3

patchesStrategicMerge:
  - deployment-patch.yaml

configMapGenerator:
  - name: app-config
    files:
      - config.properties
```

---

## Domain 3: Application Observability and Maintenance (15%)

### Understand API deprecations

**Exam Topics:**
- Kubernetes API version changes
- Deprecated API usage detection
- Migration strategies
- kubectl convert

**Platform Implementation:**

#### API Version Management
- **Component:** Kubernetes version tracking
- **Implementation:**
  - EKS cluster on recent Kubernetes version (1.28+)
  - Regular cluster upgrades following AWS guidance
  - Manifest validation in CI/CD
- **Evidence:**
  - EKS cluster configuration
  - Terraform module version constraints
- **Exam Relevance:** API deprecation awareness, version compatibility

#### Deprecation Detection
- **Component:** CI/CD validation
- **Implementation:**
  - kubectl validate in pipelines
  - Pluto scanner for deprecated APIs (future)
  - GitLab CI/CD checks before deployment
- **Evidence:**
  - CI/CD pipeline configurations
- **Exam Relevance:** Tools for deprecation detection

**Key Exam Concepts Demonstrated:**
- ✅ Awareness of API deprecations
- ✅ Version compatibility checking
- ⚠️ Active migration of deprecated APIs (as needed during upgrades)

**Study Focus:**
- Common API deprecations (apps/v1beta1 → apps/v1)
- kubectl convert usage
- API version compatibility matrix

---

### Implement probes and health checks

**Exam Topics:**
- Liveness probes
- Readiness probes
- Startup probes
- Probe types (HTTP, TCP, exec)

**Platform Implementation:**

#### Liveness Probes
- **Component:** All application deployments
- **Implementation:**
  - HTTP liveness probes on /healthz endpoint
  - Automatic pod restart on failure
  - Configured delays and thresholds
- **Evidence:**
  - Application deployment manifests
  - Health check endpoint implementations
- **Exam Relevance:** Liveness probe configuration, restart policies

#### Readiness Probes
- **Component:** Load balancer integration
- **Implementation:**
  - HTTP readiness probes on /ready endpoint
  - Prevents traffic to unhealthy pods
  - Separate from liveness (ready != live)
- **Evidence:**
  - Deployment manifests with readiness checks
  - Service endpoint management
- **Exam Relevance:** Readiness probe purpose, traffic management

#### Startup Probes (Slow-Starting Apps)
- **Component:** Applications with long initialization
- **Implementation:**
  - Startup probe for database migration apps
  - Protects against premature liveness probe failures
  - Higher failure threshold during startup
- **Evidence:**
  - Deployment manifests for complex applications
- **Exam Relevance:** Startup probe use cases, probe interaction

**Key Exam Concepts Demonstrated:**
- ✅ Liveness probe configuration
- ✅ Readiness probe configuration
- ✅ Startup probe usage
- ✅ HTTP vs TCP vs exec probes
- ✅ Probe timing parameters

**Probe Configuration Example:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        ports:
        - containerPort: 8080
        startupProbe:
          httpGet:
            path: /healthz
            port: 8080
          failureThreshold: 30
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 0
          periodSeconds: 10
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
          failureThreshold: 3
```

---

### Use built-in CLI tools to monitor Kubernetes applications

**Exam Topics:**
- kubectl logs
- kubectl describe
- kubectl get
- kubectl top
- kubectl exec

**Platform Implementation:**

#### Command-Line Monitoring
- **Component:** Daily operational tasks
- **Implementation:**
  - kubectl logs for container output
  - kubectl describe for event debugging
  - kubectl top for resource usage (metrics-server)
  - kubectl exec for debugging
- **Evidence:**
  - Operational runbooks
  - Troubleshooting procedures
  - GitHub Issue: #24 (Document platform operations)
- **Exam Relevance:** kubectl command proficiency

#### Metrics Server
- **Component:** Resource metrics collection
- **Implementation:**
  - Metrics server deployed in cluster
  - Enables kubectl top nodes/pods
  - Feeds HPA autoscaling
- **Evidence:**
  - Big Bang or standalone metrics-server deployment
- **Exam Relevance:** Resource metrics API, kubectl top

**Key Exam Concepts Demonstrated:**
- ✅ kubectl logs usage (including -f, --previous, -c)
- ✅ kubectl describe for debugging
- ✅ kubectl get with output formats (-o yaml, -o wide)
- ✅ kubectl top for resource monitoring
- ✅ kubectl exec for interactive debugging

**Essential kubectl Commands:**
```bash
# View logs
kubectl logs pod-name
kubectl logs pod-name -c container-name
kubectl logs pod-name --previous
kubectl logs -f pod-name

# Describe resources
kubectl describe pod pod-name
kubectl describe deployment deployment-name

# Get resources
kubectl get pods -o wide
kubectl get pods -o yaml
kubectl get events --sort-by='.lastTimestamp'

# Resource usage
kubectl top nodes
kubectl top pods

# Debug with exec
kubectl exec -it pod-name -- /bin/sh
kubectl exec pod-name -c container-name -- command
```

---

### Utilize container logs

**Exam Topics:**
- Container stdout/stderr
- Log viewing with kubectl
- Multi-container log access
- Previous container logs

**Platform Implementation:**

#### Centralized Logging (Prometheus + Grafana)
- **Component:** Log aggregation
- **Implementation:**
  - Container logs to stdout/stderr
  - Collected by node-level log agents
  - Aggregated in Grafana Loki (or ELK stack)
- **Evidence:**
  - Big Bang logging configuration
  - Grafana Loki dashboards
- **Exam Relevance:** Logging best practices, 12-factor app principles

#### Application Logging
- **Component:** Structured logging
- **Implementation:**
  - Applications log to stdout/stderr
  - JSON structured logs
  - Log levels (INFO, WARN, ERROR)
- **Evidence:**
  - Application logging configurations
- **Exam Relevance:** Container logging patterns

**Key Exam Concepts Demonstrated:**
- ✅ kubectl logs proficiency
- ✅ Multi-container log access
- ✅ Previous container logs (crashed containers)
- ✅ Log streaming with -f flag
- ✅ Stdout/stderr as logging destination

---

### Debugging in Kubernetes

**Exam Topics:**
- Pod troubleshooting
- Event analysis
- Resource debugging
- Temporary debugging pods

**Platform Implementation:**

#### Debugging Procedures
- **Component:** Operational runbooks
- **Implementation:**
  - Systematic debugging approach
  - Check pod status → events → logs → describe
  - Network debugging with ephemeral containers
- **Evidence:**
  - Documented troubleshooting procedures
  - GitHub Issue: #24 (Platform operations documentation)
- **Exam Relevance:** Debugging methodology

#### Ephemeral Containers for Debugging
- **Component:** Debug containers
- **Implementation:**
  - kubectl debug for running pods
  - Attach debugging tools without rebuilding images
  - Network and filesystem debugging
- **Evidence:**
  - Debugging runbooks
- **Exam Relevance:** kubectl debug command, ephemeral containers

**Key Exam Concepts Demonstrated:**
- ✅ Systematic debugging approach
- ✅ Event analysis
- ✅ Resource inspection
- ✅ Ephemeral containers
- ✅ Network debugging

**Debugging Workflow:**
```bash
# 1. Check pod status
kubectl get pods

# 2. View events
kubectl get events --field-selector involvedObject.name=pod-name

# 3. Describe pod for details
kubectl describe pod pod-name

# 4. Check logs
kubectl logs pod-name

# 5. Debug with ephemeral container
kubectl debug pod-name -it --image=busybox

# 6. Check resource usage
kubectl top pod pod-name

# 7. Exec into container if possible
kubectl exec -it pod-name -- /bin/sh
```

---

## Domain 4: Application Environment, Configuration and Security (25%)

### Discover and use resources that extend Kubernetes (CRD, Operators)

**Exam Topics:**
- Custom Resource Definitions (CRDs)
- Custom Resources (CRs)
- Operators
- API extensions

**Platform Implementation:**

#### Flux CRDs
- **Component:** GitOps Custom Resources
- **Implementation:**
  - GitRepository CRD (defines git sources)
  - Kustomization CRD (defines what to deploy)
  - HelmRelease CRD (defines Helm charts)
- **Evidence:**
  - Flux installation creates CRDs
  - Big Bang deployed via HelmRelease CR
  - GitHub Issue: #15 (Install Flux)
- **Exam Relevance:** Understanding CRDs, creating Custom Resources

#### ArgoCD Application CRD
- **Component:** ArgoCD application definitions
- **Implementation:**
  - Application CRD for declarative app deployment
  - AppProject CRD for organizing applications
  - Sync policies and health checks
- **Evidence:**
  - ArgoCD Application manifests
  - GitHub Issue: #20 (Configure ArgoCD)
- **Exam Relevance:** Using CRDs for application management

#### Istio CRDs
- **Component:** Service mesh configuration
- **Implementation:**
  - VirtualService (traffic routing)
  - DestinationRule (load balancing, TLS)
  - Gateway (ingress configuration)
- **Evidence:**
  - Big Bang Istio CRD usage
  - Service mesh configurations
- **Exam Relevance:** Complex CRD ecosystems

**Key Exam Concepts Demonstrated:**
- ✅ Understanding CRDs
- ✅ Creating and managing Custom Resources
- ✅ kubectl commands for CRDs
- ✅ Operator pattern awareness
- ✅ API group versioning

**Working with CRDs:**
```bash
# List all CRDs
kubectl get crds

# Describe a CRD
kubectl describe crd gitrepositories.source.toolkit.fluxcd.io

# List custom resources
kubectl get gitrepositories -A

# Create custom resource
kubectl apply -f custom-resource.yaml

# Get custom resource details
kubectl get gitrepository my-repo -o yaml
```

---

### Understand authentication, authorization and admission control

**Exam Topics:**
- Authentication mechanisms
- RBAC (Role-Based Access Control)
- ServiceAccount tokens
- Admission controllers

**Platform Implementation:**

#### RBAC for Platform Services
- **Component:** Role-based access control
- **Implementation:**
  - Roles and RoleBindings for namespace-level access
  - ClusterRoles and ClusterRoleBindings for cluster-level
  - Service accounts for platform services
- **Evidence:**
  - Big Bang RBAC configurations
  - ArgoCD RBAC policies
  - GitLab runner service accounts
- **Exam Relevance:** RBAC resource creation, permission management

#### ServiceAccounts
- **Component:** Pod identity
- **Implementation:**
  - Dedicated service accounts for each application
  - IRSA for AWS API access
  - Token volume mounting
- **Evidence:**
  - Application service accounts
  - Karpenter service account with IRSA
- **Exam Relevance:** ServiceAccount creation and usage

#### Admission Control
- **Component:** Gatekeeper (OPA) for policy enforcement
- **Implementation:**
  - ValidatingWebhooks for custom policies
  - MutatingWebhooks for automatic modifications
  - Policy-as-code enforcement
- **Evidence:**
  - Gatekeeper constraints (if deployed via Big Bang)
- **Exam Relevance:** Understanding admission control flow

**Key Exam Concepts Demonstrated:**
- ✅ RBAC resource creation (Role, RoleBinding, ClusterRole, ClusterRoleBinding)
- ✅ ServiceAccount usage
- ✅ Authentication vs authorization
- ⚠️ Admission controllers (understand conceptually, may not configure custom ones)

**RBAC Example:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: production
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: production
rules:
- apiGroups: [""]
  resources: ["configmaps", "secrets"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-binding
  namespace: production
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role
subjects:
- kind: ServiceAccount
  name: app-sa
  namespace: production
```

---

### Understand requests, limits, quotas

**Exam Topics:**
- Resource requests and limits
- ResourceQuotas
- LimitRanges
- QoS classes

**Platform Implementation:**

#### Pod Resource Requests and Limits
- **Component:** All application pods
- **Implementation:**
  - CPU and memory requests for scheduling
  - CPU and memory limits for enforcement
  - Guaranteed, Burstable, or BestEffort QoS
- **Evidence:**
  - Application deployment manifests
  - Platform service resource configurations
  - GitHub Issue: #26 (Performance optimization includes rightsizing)
- **Exam Relevance:** Resource specification, QoS behavior

#### Namespace ResourceQuotas
- **Component:** Namespace-level limits
- **Implementation:**
  - Total CPU/memory quotas per namespace
  - Pod count limits
  - PVC storage quotas
- **Evidence:**
  - Namespace resource quota configurations
  - Multi-tenant namespace setup
- **Exam Relevance:** ResourceQuota creation and enforcement

#### LimitRanges
- **Component:** Default resource constraints
- **Implementation:**
  - Default requests/limits for pods without spec
  - Min/max constraints per container
  - Prevents resource-hogging pods
- **Evidence:**
  - Namespace LimitRange configurations
- **Exam Relevance:** LimitRange usage, default value injection

**Key Exam Concepts Demonstrated:**
- ✅ Resource requests and limits
- ✅ QoS classes (Guaranteed, Burstable, BestEffort)
- ✅ ResourceQuota creation
- ✅ LimitRange configuration
- ✅ Resource constraint enforcement

**Resource Management Example:**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: namespace-quota
  namespace: production
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    pods: "50"
---
apiVersion: v1
kind: LimitRange
metadata:
  name: namespace-limits
  namespace: production
spec:
  limits:
  - max:
      cpu: "2"
      memory: 4Gi
    min:
      cpu: "100m"
      memory: 128Mi
    default:
      cpu: "500m"
      memory: 512Mi
    defaultRequest:
      cpu: "250m"
      memory: 256Mi
    type: Container
```

---

### Define resource requirements

**Exam Topics:**
- CPU and memory specification
- Resource units (m for millicores, Mi/Gi for memory)
- Right-sizing applications
- Resource monitoring

**Platform Implementation:**

#### Application Resource Sizing
- **Component:** Deployment resource specifications
- **Implementation:**
  - Initial estimates based on application type
  - Iterative tuning based on Prometheus metrics
  - Documented resource requirements
- **Evidence:**
  - Application deployment manifests
  - Resource utilization dashboards
  - GitHub Issue: #26 (Performance optimization)
- **Exam Relevance:** Determining appropriate resource values

#### Karpenter and Resource-Based Scaling
- **Component:** Node autoscaling
- **Implementation:**
  - Karpenter provisions nodes based on pod requests
  - Bin-packing optimization
  - Spot instance selection based on requirements
- **Evidence:**
  - Karpenter configuration
  - Node provisioning logs
- **Exam Relevance:** How resource requests drive scheduling

**Key Exam Concepts Demonstrated:**
- ✅ CPU resource specification (cores, millicores)
- ✅ Memory resource specification (bytes, Ki, Mi, Gi)
- ✅ Resource request vs limit differences
- ✅ Impact on scheduling and QoS

**Resource Specification:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        resources:
          requests:
            cpu: "250m"      # 0.25 cores
            memory: "512Mi"   # 512 mebibytes
          limits:
            cpu: "1"          # 1 core
            memory: "1Gi"     # 1 gibibyte
```

---

### Understand ConfigMaps

**Exam Topics:**
- ConfigMap creation
- Mounting ConfigMaps as volumes
- Using ConfigMaps as environment variables
- Immutable ConfigMaps

**Platform Implementation:**

#### Application Configuration
- **Component:** ConfigMaps for app settings
- **Implementation:**
  - Configuration files mounted as volumes
  - Environment variables from ConfigMaps
  - Separation of code and configuration
- **Evidence:**
  - Application ConfigMap manifests
  - Deployment volume mounts
- **Exam Relevance:** ConfigMap creation and consumption

#### Kustomize ConfigMap Generation
- **Component:** Generated ConfigMaps
- **Implementation:**
  - ConfigMaps generated from files
  - Automatic hash suffixes for rolling updates
  - Declarative configuration management
- **Evidence:**
  - Kustomization.yaml configMapGenerator sections
- **Exam Relevance:** ConfigMap generation patterns

**Key Exam Concepts Demonstrated:**
- ✅ ConfigMap creation (from literals, files, directories)
- ✅ Volume-mounted ConfigMaps
- ✅ Environment variable injection from ConfigMaps
- ✅ ConfigMap updates and pod restarts
- ✅ Immutable ConfigMaps

**ConfigMap Usage:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database.url: "postgres://db:5432/mydb"
  log.level: "info"
  config.json: |
    {
      "feature_flags": {
        "new_ui": true
      }
    }
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.url
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: log.level
        volumeMounts:
        - name: config
          mountPath: /etc/config
      volumes:
      - name: config
        configMap:
          name: app-config
          items:
          - key: config.json
            path: config.json
```

---

### Create & consume Secrets

**Exam Topics:**
- Secret creation
- Secret types (Opaque, TLS, docker-registry)
- Mounting Secrets as volumes
- Using Secrets as environment variables
- Secret encryption at rest

**Platform Implementation:**

#### Application Secrets
- **Component:** Database passwords, API keys
- **Implementation:**
  - Secrets for database credentials
  - TLS certificate secrets
  - Docker registry pull secrets
- **Evidence:**
  - Application secret manifests
  - Flux SOPS-encrypted secrets
- **Exam Relevance:** Secret creation and consumption

#### SOPS-Encrypted Secrets (Flux)
- **Component:** Encrypted secrets in Git
- **Implementation:**
  - Secrets encrypted with SOPS
  - Flux decrypts at deployment time
  - GitOps-compatible secret management
- **Evidence:**
  - Flux SOPS configuration
  - Encrypted secret files in Git
- **Exam Relevance:** Secret encryption patterns

#### TLS Secrets (Cert-Manager)
- **Component:** Certificate management
- **Implementation:**
  - Cert-manager provisions TLS certificates
  - Certificates stored as kubernetes.io/tls secrets
  - Automatic renewal
- **Evidence:**
  - Cert-manager Certificate resources
  - TLS secrets in ingress
- **Exam Relevance:** TLS secret type, certificate secrets

**Key Exam Concepts Demonstrated:**
- ✅ Secret creation (generic, tls, docker-registry)
- ✅ Base64 encoding/decoding
- ✅ Volume-mounted secrets
- ✅ Environment variable injection from secrets
- ✅ Secret types and use cases

**Secret Usage:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
stringData:
  database-password: mysecretpassword
  api-key: abc123xyz
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database-password
        volumeMounts:
        - name: secrets
          mountPath: /etc/secrets
          readOnly: true
      volumes:
      - name: secrets
        secret:
          secretName: app-secrets
```

---

### Understand ServiceAccounts

**Exam Topics:**
- ServiceAccount creation
- Default service accounts
- Pod service account assignment
- Service account tokens
- IRSA (IAM Roles for Service Accounts)

**Platform Implementation:**

#### Application Service Accounts
- **Component:** Dedicated identities for apps
- **Implementation:**
  - Each application has its own ServiceAccount
  - RBAC permissions tied to ServiceAccounts
  - Explicit service account assignment in pods
- **Evidence:**
  - Application service account manifests
  - Deployment service account references
- **Exam Relevance:** ServiceAccount creation and usage

#### IRSA for AWS Integration
- **Component:** AWS IAM integration
- **Implementation:**
  - ServiceAccounts annotated with IAM role ARNs
  - Pods assume AWS IAM roles
  - Karpenter uses IRSA for EC2 API access
- **Evidence:**
  - Karpenter service account with IRSA
  - terraform-modules/eks IRSA configuration
- **Exam Relevance:** ServiceAccount annotations, cloud provider integration

**Key Exam Concepts Demonstrated:**
- ✅ ServiceAccount creation
- ✅ Pod service account assignment
- ✅ Service account tokens
- ✅ RBAC with service accounts
- ✅ Cloud provider IAM integration (IRSA)

**ServiceAccount Example:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: production
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/app-role
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  namespace: production
spec:
  template:
    spec:
      serviceAccountName: app-sa
      containers:
      - name: app
        image: myapp:latest
```

---

### Understand Application Security (SecurityContexts, Capabilities, etc.)

**Exam Topics:**
- Pod SecurityContext
- Container SecurityContext
- RunAsUser, RunAsGroup, FSGroup
- Capabilities (add/drop)
- Privileged containers
- Read-only root filesystem

**Platform Implementation:**

#### Non-Root Containers
- **Component:** All application containers
- **Implementation:**
  - Containers run as non-root user
  - SecurityContext specifies UID/GID
  - Prevents privilege escalation
- **Evidence:**
  - Application Dockerfiles (USER directive)
  - Deployment security contexts
- **Exam Relevance:** SecurityContext configuration

#### Read-Only Root Filesystem
- **Component:** Immutable containers
- **Implementation:**
  - Root filesystem mounted read-only
  - Writable volumes for necessary paths
  - Enhanced security posture
- **Evidence:**
  - SecurityContext readOnlyRootFilesystem: true
- **Exam Relevance:** Filesystem restrictions

#### Capability Management
- **Component:** Linux capabilities
- **Implementation:**
  - Drop all capabilities by default
  - Add only required capabilities
  - Minimal permission model
- **Evidence:**
  - SecurityContext capabilities configuration
- **Exam Relevance:** Capability add/drop

**Key Exam Concepts Demonstrated:**
- ✅ Pod and container SecurityContext
- ✅ RunAsUser, RunAsNonRoot
- ✅ FSGroup for volume permissions
- ✅ Capabilities management
- ✅ Read-only root filesystem
- ✅ Privilege escalation prevention

**Security Context Example:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  template:
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        runAsGroup: 3000
        fsGroup: 2000
      containers:
      - name: app
        image: myapp:latest
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL
            add:
            - NET_BIND_SERVICE
        volumeMounts:
        - name: tmp
          mountPath: /tmp
      volumes:
      - name: tmp
        emptyDir: {}
```

---

## Domain 5: Services and Networking (20%)

### Demonstrate basic understanding of NetworkPolicies

**Exam Topics:**
- NetworkPolicy creation
- Ingress and egress rules
- Pod selectors
- Namespace selectors
- Default deny policies

**Platform Implementation:**

#### Istio Network Policies
- **Component:** Service mesh network controls
- **Implementation:**
  - Istio AuthorizationPolicies for L7 policies
  - Kubernetes NetworkPolicies for L3/L4
  - Default deny, explicit allow model
- **Evidence:**
  - Big Bang Istio network policy configurations
  - Application namespace network policies
- **Exam Relevance:** NetworkPolicy syntax and behavior

#### Namespace Isolation
- **Component:** Multi-tenant security
- **Implementation:**
  - Default deny NetworkPolicy per namespace
  - Explicit allow policies for required communication
  - Cross-namespace policy rules
- **Evidence:**
  - Namespace NetworkPolicy manifests
- **Exam Relevance:** Namespace-based network segmentation

**Key Exam Concepts Demonstrated:**
- ✅ NetworkPolicy creation
- ✅ Ingress and egress rule syntax
- ✅ Pod selector usage
- ✅ Namespace selector usage
- ✅ Default deny patterns
- ⚠️ Advanced scenarios (understand but may not implement all)

**NetworkPolicy Example:**
```yaml
# Default deny all ingress
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
---
# Allow specific ingress
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
---
# Allow egress to database
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-backend-to-db
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Egress
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: database
      podSelector:
        matchLabels:
          app: postgres
    ports:
    - protocol: TCP
      port: 5432
```

---

### Provide and troubleshoot access to applications via services

**Exam Topics:**
- Service types (ClusterIP, NodePort, LoadBalancer)
- Service selectors
- Endpoints
- Service DNS
- Troubleshooting service connectivity

**Platform Implementation:**

#### ClusterIP Services (Internal)
- **Component:** Internal service-to-service communication
- **Implementation:**
  - ClusterIP services for all applications
  - DNS-based service discovery
  - Load balancing across pod endpoints
- **Evidence:**
  - Application service manifests
  - Internal service communication
- **Exam Relevance:** ClusterIP creation, DNS resolution

#### LoadBalancer Services (External Access)
- **Component:** Istio ingress gateway
- **Implementation:**
  - LoadBalancer service for Istio ingress
  - AWS NLB provisioned automatically
  - External traffic entry point
- **Evidence:**
  - Istio ingress gateway service
  - AWS NLB in terraform/AWS console
- **Exam Relevance:** LoadBalancer service type, cloud provider integration

#### Service Troubleshooting
- **Component:** Debugging service connectivity
- **Implementation:**
  - Check service endpoints
  - Verify pod selectors
  - Test DNS resolution
  - Validate network policies
- **Evidence:**
  - Troubleshooting runbooks
  - Operational procedures
- **Exam Relevance:** Service debugging methodology

**Key Exam Concepts Demonstrated:**
- ✅ Service creation (ClusterIP, NodePort, LoadBalancer)
- ✅ Service selectors and endpoints
- ✅ Service DNS (service.namespace.svc.cluster.local)
- ✅ Service troubleshooting
- ✅ Port mapping (port, targetPort, nodePort)

**Service Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: production
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-external
  namespace: production
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 3000
```

**Service Troubleshooting Commands:**
```bash
# Check service
kubectl get svc backend

# Check endpoints
kubectl get endpoints backend

# Describe service for events
kubectl describe svc backend

# Test DNS from pod
kubectl run -it --rm debug --image=busybox --restart=Never -- nslookup backend.production.svc.cluster.local

# Test connectivity
kubectl run -it --rm debug --image=busybox --restart=Never -- wget -O- http://backend.production.svc.cluster.local
```

---

### Use Ingress rules to expose applications

**Exam Topics:**
- Ingress resource creation
- Ingress controllers
- Path-based routing
- Host-based routing
- TLS termination

**Platform Implementation:**

#### Istio Ingress Gateway
- **Component:** Ingress traffic management
- **Implementation:**
  - Istio Gateway for L4-L6 configuration
  - VirtualService for L7 routing
  - TLS termination at gateway
- **Evidence:**
  - Big Bang Istio ingress configuration
  - Application Gateway and VirtualService resources
- **Exam Relevance:** Advanced ingress patterns

#### Kubernetes Ingress (Alternative)
- **Component:** Standard Ingress resources
- **Implementation:**
  - Ingress resources for HTTP/HTTPS routing
  - Path-based and host-based routing
  - Cert-manager for TLS certificate provisioning
- **Evidence:**
  - Application Ingress manifests
  - Cert-manager Certificate resources
- **Exam Relevance:** Ingress resource syntax

**Key Exam Concepts Demonstrated:**
- ✅ Ingress resource creation
- ✅ Path-based routing
- ✅ Host-based routing (virtual hosts)
- ✅ TLS configuration
- ✅ Ingress controller awareness
- ✅ Cert-manager integration

**Ingress Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: production
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  ingressClassName: istio
  tls:
  - hosts:
    - app.zavestudios.com
    secretName: app-tls
  rules:
  - host: app.zavestudios.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
```

---

## Exam Preparation Strategy

### How to Use This Mapping

**1. Hands-On Learning Loop:**
```
For each application you build:
├─ Implement CKAD concepts in manifests
├─ Test functionality in cluster
├─ Document what you learned
└─ Map to exam objectives in this document
```

**2. Study Priority by Domain Weight:**

High priority (25%):
- **Domain 4:** Application Environment, Configuration and Security
  - ConfigMaps and Secrets (used daily)
  - SecurityContext (security hardening)
  - RBAC (platform operations)

Medium-high priority (20% each):
- **Domain 1:** Application Design and Build
  - Container images (every app)
  - Multi-container pods (Istio sidecars everywhere)
  - Workload resources (Deployments, CronJobs)

- **Domain 2:** Application Deployment
  - Helm (Big Bang and apps)
  - Kustomize (Flux usage)
  - Deployment strategies (rolling updates)

- **Domain 5:** Services and Networking
  - Services (every app needs one)
  - Ingress (external access)
  - NetworkPolicies (security)

Medium priority (15%):
- **Domain 3:** Application Observability and Maintenance
  - Probes (health checks)
  - Logs and debugging (troubleshooting)
  - kubectl commands (daily usage)

**3. Gap Analysis:**

**Strong Coverage (learn by doing):**
- ✅ Deployments and rolling updates (used constantly)
- ✅ ConfigMaps and Secrets (every app)
- ✅ Services and Ingress (all apps exposed)
- ✅ Multi-container pods (Istio sidecars)
- ✅ Helm and Kustomize (platform foundation)
- ✅ Probes and health checks (production readiness)
- ✅ RBAC and ServiceAccounts (security)
- ✅ SecurityContext (container security)

**Moderate Coverage (some hands-on, study details):**
- ⚠️ StatefulSets (platform uses them, create your own for practice)
- ⚠️ Jobs (understand vs CronJobs)
- ⚠️ NetworkPolicies (Istio provides, practice K8s native)
- ⚠️ ResourceQuotas and LimitRanges (understand concepts)
- ⚠️ CRDs and Operators (use many, create simple one for practice)

**Study Separately:**
- 📚 API deprecations (track Kubernetes release notes)
- 📚 Advanced debugging techniques (ephemeral containers)
- 📚 Adapter and Ambassador patterns (understand conceptually)

**4. Practice Exam Scenarios:**

Create mini-projects that combine multiple concepts:

**Scenario 1: Stateful Application**
- StatefulSet with persistent storage
- ConfigMap for configuration
- Secret for database password
- Readiness/liveness probes
- NetworkPolicy for security
- Service for internal access

**Scenario 2: Scheduled Job**
- CronJob for periodic task
- ConfigMap for job configuration
- Secret for API credentials
- Init container for setup
- Resource requests/limits
- ServiceAccount with RBAC

**Scenario 3: Multi-Container Application**
- Deployment with app + sidecar
- Shared volume between containers
- ConfigMap mounted as volume
- Liveness probe on main container
- Service exposing the application
- Ingress for external access

---

## Platform Implementation Roadmap vs CKAD Study

### Phase 1: Bootstrap (Month 1)
**Build:** Infrastructure foundation  
**CKAD Study:** Limited (cluster setup focus)  
**Prepare:** Environment for CKAD practice

### Phase 2: Platform (Month 2)
**Build:** Big Bang, GitOps, monitoring  
**CKAD Study:** Heavy study period
- **Focus Domains:** 2 (Helm/Kustomize), 4 (CRDs, RBAC, Security)
- **Hands-On:** Big Bang CRDs, Flux resources, RBAC configs
- **Practice:** Platform service configurations

### Phase 3: Applications (Month 3)
**Build:** Application workloads  
**CKAD Study:** Peak hands-on practice
- **Focus Domains:** 1 (containers, workloads, volumes), 3 (probes, logs, debugging), 5 (services, ingress)
- **Hands-On:** Every app deployment reinforces exam concepts
- **Practice:** Real production-like scenarios

### Phase 4: Optimization (Month 4)
**Build:** Performance tuning, security hardening  
**CKAD Study:** Final review and exam
- **Focus Domains:** 4 (SecurityContext, resources), 3 (monitoring)
- **Review:** All domains with practice exams
- **Exam Target:** Take CKAD exam

---

## CKAD Exam Preparation Resources

### Official Resources
- [ ] Kubernetes Documentation (kubernetes.io/docs)
- [ ] CKAD Exam Curriculum (this curriculum document)
- [ ] Kubernetes The Hard Way (kelseyhightower/kubernetes-the-hard-way)
- [ ] kubectl Cheat Sheet (kubernetes.io/docs/reference/kubectl/cheatsheet)

### Practice Platforms
- [ ] Killer.sh (official CKAD practice exam - 2 sessions included with exam)
- [ ] KodeKloud CKAD Course (hands-on labs)
- [ ] Linux Foundation CKAD Course (optional)

### Hands-On Practice
- [ ] EKS cluster (ZaveStudios platform)
- [ ] kubectl muscle memory (daily usage)
- [ ] Vim/nano proficiency (exam uses terminal only)
- [ ] YAML formatting speed (write manifests fast)
- [ ] kubectl imperative commands (faster than YAML for exam)

### Time Management for Exam
- 2 hours for ~15-20 questions
- ~6-8 minutes per question average
- Skip hard questions, come back later
- Use kubectl imperative commands when possible
- Copy/paste from documentation (allowed)

---

## kubectl Imperative Commands Cheat Sheet

**For CKAD exam, imperative commands are faster than writing YAML:**

```bash
# Create Deployment
kubectl create deployment app --image=nginx --replicas=3

# Expose as Service
kubectl expose deployment app --port=80 --target-port=8080 --type=ClusterIP

# Create ConfigMap
kubectl create configmap app-config --from-literal=key1=value1

# Create Secret
kubectl create secret generic app-secret --from-literal=password=abc123

# Create Job
kubectl create job test-job --image=busybox -- echo "Hello"

# Create CronJob
kubectl create cronjob test-cron --image=busybox --schedule="*/5 * * * *" -- echo "Hello"

# Generate YAML without creating
kubectl create deployment app --image=nginx --replicas=3 --dry-run=client -o yaml > deployment.yaml

# Set image
kubectl set image deployment/app nginx=nginx:1.21

# Scale deployment
kubectl scale deployment app --replicas=5

# Create ServiceAccount
kubectl create serviceaccount app-sa

# Create Role
kubectl create role app-role --verb=get,list --resource=pods

# Create RoleBinding
kubectl create rolebinding app-binding --role=app-role --serviceaccount=default:app-sa
```

---

## Success Metrics

### Platform Implementation
- [ ] All applications deployed with proper CKAD patterns
- [ ] Comprehensive manifests demonstrating exam concepts
- [ ] Operational platform proving hands-on experience

### CKAD Exam Preparation
- [ ] All exam domains mapped to platform components
- [ ] Hands-on practice with every exam objective
- [ ] kubectl command muscle memory
- [ ] Practice exam score >80%
- [ ] CKAD certification earned

### Portfolio Integration
- [ ] Platform demonstrates CKAD concepts in production context
- [ ] Can explain any Kubernetes decision in CKAD terminology
- [ ] Kubernetes expertise validated by certification
- [ ] Hands-on experience proven by running platform

---

**Last Updated:** December 31, 2024  
**Next Review:** After Phase 2 completion  
**Exam Target:** Q2 2025 (after platform applications deployed)
