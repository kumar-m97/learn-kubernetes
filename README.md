# All about Kuberntes! 

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

<img width="1100" height="535" alt="image" src="https://github.com/user-attachments/assets/091022a0-720a-4349-9883-ddf91d26be76" />

**Master Node:** 
The Master Node is responsible for making global decisions about the cluster, such as scheduling new pods, 
monitoring the health of nodes and pods, and scaling applications based on demand.


The Master Node consists of several key components:
**API Server:** This is the central management point of the cluster. It exposes the Kubernetes API, which allows users and other components to interact with the cluster.
**etcd:** It is a distributed key-value store that stores the configuration data of the cluster. All information about the cluster’s state is stored here.
**Controller Manager: **The Controller Manager includes several controllers that watch the cluster state through the API Server and take corrective actions to ensure the desired state is maintained. For example, the ReplicaSet controller ensures the specified number of pod replicas are running.
**Scheduler:** The Scheduler is responsible for assigning new pods to nodes based on resource requirements and availability. It helps distribute the workload evenly across the worker nodes.

**Worker Nodes:**
The Worker Nodes are the machines where containers (pods) are scheduled and run. They form the data plane of the cluster, executing the actual workloads. 
Each Worker Node runs several key components:

**Kubelet:** The Kubelet is the agent that runs on each Worker Node and communicates with the Master Node. It ensures that the containers described in the pod specifications are running and healthy.
**Container Runtime:** Kubernetes supports multiple container runtimes, such as Docker or containerd. The Container Runtime is responsible for pulling container images and running containers on the Worker Nodes.
**Kube Proxy:** Kube Proxy is responsible for network communication within the cluster. It manages the network routing for services and performs load balancing.

🚀 When a user deploys a new application in Kubernetes — What happens?
Step 1: User submits Deployment YAML ---- kubectl apply -f deployment.yaml
-> The kubectl CLI sends this request to the API Server (the control plane entry point).
-> The API Server validates and stores the desired state in etcd.

Step 2: Scheduler decides where to run Pods
-> The Kubernetes Scheduler checks for pending Pods (not yet assigned to a node).
-> It chooses the best worker node based on:
CPU/RAM availability
Node taints/labels
Affinity rules
-> The Scheduler assigns the Pod to the chosen node.

Step 3: Kubelet on Worker Node creates Pod
-> The Kubelet on that node reads the Pod spec from API Server.
-> It asks the container runtime (Docker, containerd) to pull images and start the container.
-> A Pod is now running on the node.

Step 4: Pod status updated and monitored
-> The Kubelet sends Pod updates (Running/Failed/CrashLoop) to the API Server.
-> ReplicaSet/Deployment Controller ensures the desired number of Pods are always running

**Main K8s Components & Objects**
**Nodes:**
In Kubernetes, a Node is a worker machine where containers are deployed and run. Each node represents an individual machine within the cluster, and it could be a physical or virtual machine. Nodes are responsible for running the actual workloads and providing the necessary resources to run containers.

**🗂 Basic File Structure of Any Kubernetes Manifest**
<img width="852" height="392" alt="image" src="https://github.com/user-attachments/assets/41f94c24-6ceb-4d23-b52f-7cc34b0c9c62" />

Note:
Lables -- key-value pairs attached to Kubernetes objects like Pods, Deployments, Nodes, Services, etc. to label them with unique keywords
Selectors -- used to find and match objects that have specific labels

***Pods:***
A Pod is the smallest deployable unit in Kubernetes and represents one or more tightly coupled containers. Containers within a pod share the same network namespace, enabling them to communicate with each other over localhost. A pod represents a single instance of a process in the cluster.

***Deployment:***
A Kubernetes Deployment is a higher-level abstraction that manages a set of identical pods. Deployments provide features like rolling updates, rollback, and scaling, making them suitable for web servers, APIs, and microservices.

Check deployment.yml FYR

***Service:***
Service is another Kubernetes object to access a deployment. It allows us to expose our application to other pods within the cluster or to external clients.Services provide load balancing and automatic scaling for the pods behind them.

check service.yml FYR


***Ingress:***
Ingress provides a way to expose your services to external clients outside the cluster. It acts as an external entry point to your applications and enables you to configure routing rules and load balancing for incoming traffic. For the Ingress resource to work, you need an Ingress controller deployed in your Kubernetes cluster. 

Check ingress.yml FYR

