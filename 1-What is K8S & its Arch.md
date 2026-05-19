What Is Kubernetes (K8s)?
-------------------------
Kubernetes is an open‑source, distributed system that automates deployment, scaling, and management of containerized applications across clusters of machines.

But that sentence hides a massive architecture underneath.

**High‑Level Architecture Overview**
----

A Kubernetes cluster is made of two major layers:

**1. Control Plane (Master Layer)**

The “brain”  that  makes decisions, stores cluster state, schedules workloads, and maintains desired state.

**2. Worker Nodes (Data Plane)**
The “hands” — run your applications inside Pods.

Together, they form a Kubernetes Cluster — a self‑healing, scalable, distributed system.

**🏛️ Kubernetes Control Plane**

The control plane ensures the cluster always matches the desired state (what you declare in YAML).

It consists of four core components:

1️⃣ kube‑apiserver — The Front Door of Kubernetes
The API Server is the central communication hub.
Everything — kubectl, controllers, nodes — talks to the cluster only through the API server.

Responsibilities:
Validates all requests
Authenticates & authorizes users
Stores cluster state in etcd
Exposes REST/gRPC APIs
Provides watch streams for real‑time updates
Why it matters:
Without the API server, nothing in Kubernetes can communicate.

2️⃣ etcd — The Cluster Database
A distributed, strongly consistent key‑value store.
It stores everything about your cluster: Pods, Deployments, Secrets, ConfigMaps, Nodes, Events.

Key properties:
Highly available
Strong consistency (Raft algorithm)
Source of truth
If it’s not in etcd, it does not exist in Kubernetes.

3️⃣ kube‑scheduler — The Brain That Places Pods
Watches for Pods with no assigned node and decides where to run them.

Scheduling decisions consider:
CPU/memory requirements
Node resource availability
Affinity/anti‑affinity
Taints & tolerations
Data locality
Deadlines
This is where Kubernetes becomes intelligent.

4️⃣ kube‑controller‑manager — The Automation Engine
Runs all controllers that maintain the cluster’s desired state.

Examples of controllers:
Node Controller — detects node failures
Replication Controller — ensures correct number of Pods
Endpoint Controller — manages Service endpoints
Job Controller — manages Jobs
Each controller is a loop that constantly checks:

“Is the actual state equal to the desired state?”
If not → it fixes it.

🖥️ Worker Node Architecture — Where Your Apps Run
Each worker node runs three critical components:

1️⃣ Container Runtime
Responsible for running containers.
Examples: containerd, CRI‑O, Docker (deprecated)

Responsibilities:
Pull images
Start/stop containers
Manage container lifecycle
2️⃣ kubelet — The Node Agent
Runs on every node.
It ensures containers are running exactly as the API server instructs.

Responsibilities:
Registers node with API server
Executes PodSpecs
Reports node & pod health
Manages container runtime
If kubelet dies → the node becomes “NotReady”.

3️⃣ kube‑proxy — The Networking Brain
Maintains network rules on each node.

Responsibilities:
Implements Service networking
Load balances traffic to Pods
Manages iptables/ipvs rules
This is how Pods communicate inside and outside the cluster.

📦 Kubernetes Workload Objects (Data Plane)
These are not “architecture components” but essential to understanding how Kubernetes works.

Pod
Smallest deployable unit — contains one or more containers.

ReplicaSet
Ensures a specific number of Pods are running.

Deployment
Manages ReplicaSets → rolling updates, rollbacks.

DaemonSet
Runs one Pod per node (e.g., logging agents).

StatefulSet
For stateful apps → stable network identity & storage.

Job / CronJob
Run tasks once or on schedule.

🌐 Cluster Networking Architecture
Kubernetes networking has four rules:

Every Pod gets its own IP
Pods can communicate without NAT
Nodes can communicate with Pods
Services provide stable virtual IPs
kube‑proxy + CNI plugins (Calico, Flannel, Cilium) implement this.

🧩 Add‑Ons (Optional but Common)
Add‑ons extend cluster functionality.
Examples:

DNS (CoreDNS)
Ingress Controller
Metrics Server
Dashboard
🔄 How Kubernetes Works Internally — Full Workflow
Let’s walk through a real example:

You run:
kubectl apply -f deployment.yaml
Step‑by‑step:
kubectl → API Server
Sends request to create Deployment.

API Server → etcd
Stores desired state.

Deployment Controller
Sees new Deployment → creates ReplicaSet.

ReplicaSet Controller
Creates Pods.

Scheduler
Assigns Pods to nodes.

kubelet on each node
Pulls images → starts containers.

kube‑proxy
Updates networking rules.

Cluster reaches desired state
Self‑healing begins.

🧱 Complete Kubernetes Architecture Diagram (Text Version)
                +---------------------------+
                |       Control Plane       |
                |---------------------------|
                |  kube-apiserver           |
                |  etcd                     |
                |  kube-scheduler           |
                |  controller-manager       |
                +-------------+-------------+
                              |
                              |
                +-------------v-------------+
                |         Worker Nodes       |
+---------------+----------------------------+---------------+
|  kubelet      |  kube-proxy   | container runtime (CRI)    |
|  Pods (containers)            |                            |
+-------------------------------------------------------------+
🟦 Summary Table — Control Plane vs Worker Node
Component	Layer	Purpose
kube‑apiserver	Control Plane	API gateway, validation, communication
etcd	Control Plane	Cluster database
kube‑scheduler	Control Plane	Assigns Pods to nodes
controller‑manager	Control Plane	Maintains desired state
kubelet	Worker Node	Runs containers, reports health
kube‑proxy	Worker Node	Networking & load balancing
Container Runtime	Worker Node	Executes containers
⭐ Final Takeaway
Kubernetes = a distributed, self‑healing, declarative system that turns many machines into one unified compute platform.
