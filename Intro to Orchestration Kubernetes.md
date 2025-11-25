

**Kubernetes** (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications across clusters of hosts. It provides a robust framework for running distributed systems resiliently.

---

## Core Concepts

### Pods

> [!info] Definition A **Pod** is the smallest deployable unit in Kubernetes. It represents one or more containers that are scheduled together on the same node and share the same network namespace.

#### Key Characteristics

- **Atomic Unit**: Pods are created, scheduled, and destroyed as a single entity
- **Co-location**: All containers in a pod run on the same worker node
- **Shared Resources**: Containers within a pod share:
    - Network namespace (IP address and port space)
    - Storage volumes
    - Process namespace (optional)

#### Pod Lifecycle

1. **Creation**: User/Controller creates pod specification
2. **Scheduling**: Control plane schedules pod to a worker node
3. **Execution**: Kubelet on the worker node creates and runs containers
4. **Termination**: When deleted, all associated containers are terminated and resources released

> [!note] Important The control plane assigns scheduling tasks but doesn't execute workloads—execution happens on the data plane (worker nodes).

---

## Kubernetes Architecture

Kubernetes follows a **master-worker** architecture divided into two main planes:

### Control Plane (Master Components)

The control plane manages the cluster and makes global decisions about scheduling, scaling, and responding to cluster events.

#### 1. API Server

- **Role**: Frontend of the Kubernetes control plane
- **Functionality**:
    - Handles all RESTful API requests
    - Processes internal and external requests
    - Validates and processes API objects
    - Gateway for all cluster operations
- **Communication**: Entry point for kubectl, dashboard, and other tools

#### 2. etcd (Distributed Key-Value Store)

- **Role**: Cluster's database and source of truth
- **Functionality**:
    - Stores all cluster configuration data
    - Maintains cluster state information
    - Provides consistent, distributed data storage
    - Stores configuration, secrets, and service discovery information
- **Type**: Consistent and highly-available key-value store

#### 3. Controller Manager

- **Role**: Maintains desired cluster state
- **Functionality**:
    - Continuously monitors cluster state via API server
    - Compares actual state vs. desired state
    - Takes corrective action when discrepancies exist
    - Manages replica counts for deployments
- **Examples of Controllers**:
    - Node Controller
    - Replication Controller
    - Endpoints Controller
    - Service Account & Token Controllers

#### 4. Scheduler

- **Role**: Pod placement decision maker
- **Functionality**:
    - Assigns pods to appropriate worker nodes
    - Considers multiple factors:
        - Resource requirements (CPU, memory)
        - Hardware/software constraints
        - Affinity and anti-affinity specifications
        - Data locality
        - Workload deadlines
    - Does not actually run the pods (delegates to kubelet)

---

### Data Plane (Worker Node Components)

The data plane consists of worker nodes that run containerized applications.

#### 1. Kubelet

- **Role**: Primary node agent
- **Functionality**:
    - Ensures containers are running in pods
    - Monitors pod health and reports to control plane
    - Executes pod specifications (PodSpecs)
    - Manages pod lifecycle on the node
    - Communicates with API server
- **Scope**: Runs on every worker node

#### 2. Container Runtime Interface (CRI)

- **Role**: Container lifecycle management
- **Functionality**:
    - Pulls container images from registries
    - Creates and starts containers
    - Stops and deletes containers
    - Manages container resources
- **Supported Runtimes**:
    - containerd
    - CRI-O
    - Docker (deprecated in newer versions)

#### 3. Kube-Proxy

- **Role**: Network proxy and load balancer
- **Functionality**:
    - Maintains network rules on nodes
    - Implements service abstraction
    - Handles routing and load balancing
    - Manages iptables/IPVS rules
    - Enables pod-to-pod and external communication

---

## Key Features

### 1. Automatic Bin Packing

- **Description**: Intelligently places containers based on resource requirements
- **Benefits**:
    - Optimizes resource utilization
    - Maximizes efficiency without sacrificing availability
    - Considers CPU and memory constraints
    - Balances workload across nodes

### 2. Self-Healing

- **Capabilities**:
    - Automatically restarts failed containers
    - Replaces containers when they die
    - Reschedules pods when nodes fail
    - Kills non-responsive containers (based on health checks)
- **Health Checks**:
    - Liveness probes (is container alive?)
    - Readiness probes (is container ready to serve traffic?)
    - Startup probes (has container started successfully?)

### 3. Horizontal Scaling

- **Methods**:
    - Manual scaling via kubectl commands
    - UI-based scaling through dashboard
    - Automatic scaling based on metrics (CPU, memory, custom metrics)
- **Scaling Options**:
    - Scale deployments up/down
    - Set minimum and maximum replica counts
    - Configure scaling policies and thresholds

### 4. Service Discovery and Load Balancing

- **Features**:
    - Each pod receives its own IP address
    - Services get a single DNS name for a set of pods
    - Built-in load balancing across pod replicas
    - No application modification required
    - ClusterIP, NodePort, LoadBalancer service types

### 5. Automated Rollouts and Rollbacks

- **Rolling Updates**:
    - Updates application configuration with zero downtime
    - Gradual replacement of old pods with new ones
    - Configurable update strategy (maxSurge, maxUnavailable)
- **Rollbacks**:
    - Quick reversion to previous stable state
    - Maintains revision history
    - One-command rollback capability

### 6. Secret and Configuration Management

- **Secrets**: Securely store sensitive data (passwords, tokens, keys)
- **ConfigMaps**: Store non-sensitive configuration data
- **Benefits**:
    - Deploy/update without rebuilding images
    - Keep secrets out of code repository
    - Environment-specific configurations
    - Dynamic configuration updates

### 7. Batch Execution

- **Support for**:
    - Batch jobs (run to completion)
    - Cron jobs (scheduled/periodic jobs)
    - CI/CD workloads
- **Features**:
    - Parallel job execution
    - Job completion tracking
    - Automatic retry on failure

---

## Scaling Strategies

### 1. Horizontal Pod Autoscaler (HPA)

- **Function**: Adjusts number of pod replicas
- **Triggers**:
    - CPU utilization
    - Memory usage
    - Custom metrics (requests per second, queue length)
    - External metrics
- **Behavior**:
    - Automatically scales pods up/down
    - Based on observed metrics
    - Configurable scaling policies

### 2. Vertical Pod Autoscaler (VPA)

- **Function**: Adjusts pod resource requests/limits
- **Optimizes**:
    - CPU allocation
    - Memory (RAM) allocation
- **Benefits**:
    - Improves resource efficiency
    - Right-sizes pods automatically
    - Reduces resource waste

### 3. Cluster Autoscaler

- **Function**: Adjusts number of nodes in cluster
- **Triggers**:
    - Pending pods that cannot be scheduled
    - Insufficient cluster resources
    - Node underutilization
- **Behavior**:
    - Adds nodes when pods can't be scheduled
    - Removes nodes when underutilized
    - Works with cloud providers (AWS, GCP, Azure)

---

## Practical Insights

### Certificate Management

- **Dynamic Management**: Kubernetes handles certificate lifecycle automatically
- **Use Cases**:
    - Authentication between components
    - TLS for API server communication
    - Pod-to-pod encrypted communication
- **Benefits**:
    - Reduces security risks
    - Eliminates static credential management
    - Automatic certificate rotation

### Pod Lifecycle Management

- **Graceful Termination**:
    1. Pod deletion request received
    2. Pod enters "Terminating" state
    3. PreStop hooks executed (if configured)
    4. SIGTERM signal sent to containers
    5. Grace period for cleanup (default 30s)
    6. SIGKILL sent if not terminated
    7. Pod removed from API server
    8. Resources released
- **Resource Cleanup**:
    - Automatic cleanup of associated containers
    - Efficient resource release
    - Network rules updated
    - Volumes unmounted

---

## Architecture Diagram (Conceptual)

```
┌─────────────────── Control Plane ───────────────────┐
│                                                      │
│  ┌────────────┐  ┌──────┐  ┌────────────┐         │
│  │ API Server │  │ etcd │  │ Scheduler  │         │
│  └────────────┘  └──────┘  └────────────┘         │
│                                                      │
│  ┌──────────────────┐                              │
│  │ Controller Mgr   │                              │
│  └──────────────────┘                              │
└──────────────────────────────────────────────────────┘
                      │
                      │ Communication
                      │
┌─────────────────── Data Plane ──────────────────────┐
│                                                      │
│  Worker Node 1          Worker Node 2               │
│  ┌─────────────┐        ┌─────────────┐            │
│  │   Kubelet   │        │   Kubelet   │            │
│  │ Kube-Proxy  │        │ Kube-Proxy  │            │
│  │     CRI     │        │     CRI     │            │
│  │   [Pods]    │        │   [Pods]    │            │
│  └─────────────┘        └─────────────┘            │
└──────────────────────────────────────────────────────┘
```

---

