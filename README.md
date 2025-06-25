# K8s

## Containers

- Lets take a senario that I am developing an end to end applicatuon where it needs node, mongo, redis, ansible..

- Now the problem is they have their own dependencies and own libraries with different versions and all togather wont support the same version and same OS compatibility.

- So we come up with a solution that is container. Where we can host our individual application on individual container. so there wont be any any interruption with version mis match and all the containers are on the same VM and each container has their own dependencies and libraries as they needed.

## Container Orchestration

Container orchestration is the automated management of containers, especially when they are deployed across many systems. It handles:

Deployment

Scaling (up/down)

Networking

Monitoring

Health checks

Load balancing

Self-healing (e.g., restarting failed containers)

This becomes crucial in production environments where there may be hundreds or thousands of containers running microservices.

## Kubernetes

- Kubernetes (also known as K8s) is an open-source container orchestration platform originally developed by Google.

| Component              | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| **Pod**                | Smallest deployable unit in K8s; it can hold one or more containers |
| **Node**               | A VM or physical machine that runs pods                             |
| **Cluster**            | A group of nodes                                                    |
| **Deployment**         | Defines how to deploy and manage pods                               |
| **Service**            | Exposes a set of pods as a network service (with stable IP/DNS)     |
| **ReplicaSet**         | Ensures a specified number of pod replicas are running              |
| **Scheduler**          | Decides on which node a pod should run                              |
| **Controller Manager** | Ensures the desired state (like number of pods) is maintained       |
| **Kubelet**            | Agent running on each node that communicates with the control plane |
| **etcd**               | Key-value store for storing cluster state                           |

## CMP ECS with K8s

| **Concept**                | **Kubernetes**                                 | **Amazon ECS**                                              | **Explanation**                                                                                    |
| -------------------------- | ---------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Cluster**                | `Cluster`                                      | `Cluster`                                                   | Group of EC2/Fargate instances (nodes) where containers run                                        |
| **Workload Definition**    | `Pod`                                          | `Task`                                                      | The basic unit of deployment. ECS Task is similar to a Pod (which may include multiple containers) |
| **Container Definition**   | `Pod Template` (inside Deployment/StatefulSet) | `Task Definition`                                           | Describes the containers to run, image, CPU/memory, ports                                          |
| **Deployment Management**  | `Deployment`, `StatefulSet`, `DaemonSet`       | `Service` (with optional `Task Set` in ECS with CodeDeploy) | Defines how containers are deployed and managed over time                                          |
| **Scaling & Load Mgmt**    | `ReplicaSet` (used by Deployment)              | `Desired Count` in ECS Service                              | Specifies how many copies of a pod/task to run                                                     |
| **Service Discovery / LB** | `Service`                                      | `Service` + `Target Group` + `Load Balancer`                | Exposes pods/tasks internally or externally                                                        |
| **Scheduler**              | `kube-scheduler`                               | ECS Scheduler (built-in or Capacity Provider strategy)      | Decides which node will run the task/pod                                                           |
| **State Store**            | `etcd`                                         | ECS internal metadata store (AWS managed)                   | Stores cluster state                                                                               |
| **Node Agent**             | `kubelet`                                      | ECS agent (running on EC2, not needed for Fargate)          | Runs on nodes to manage containers                                                                 |
| **Networking**             | `CNI` plugin (Calico, Flannel, etc.)           | VPC Networking (awsvpc, bridge, host modes)                 | Container networking for communication                                                             |
| **Monitoring & Logging**   | Prometheus, Grafana, Fluentd, etc.             | CloudWatch Logs, Container Insights                         | Logs and metrics                                                                                   |

| Use Case                                   | Kubernetes                        | ECS                                 |
| ------------------------------------------ | --------------------------------- | ----------------------------------- |
| You want to run a container                | `Pod`                             | `Task`                              |
| You want to define how to run container(s) | `Deployment` + `Pod Template`     | `Task Definition`                   |
| You want to scale out                      | Set `replicas: 3` in Deployment   | Set `desiredCount: 3` in Service    |
| You want high availability                 | Run pods across multiple nodes    | Run tasks in ECS service across AZs |
| You want to expose app                     | `Service` (NodePort/LoadBalancer) | `Service` + ALB/NLB                 |

## MAster Node

- Master Node is a node where the kuberenetes is installed in it
- It watches all the children nodes and it is responsible for the management of children nodes orchestration
- It is the only node where the API server is installed.
- This differentiate it from wroker nodes.
- It has etcd , controller, scheduler installed

## Components

### API Server

- Acts as frontend for the kubernetes
- The users or CLI's talk with the API server to interact with kubernetes cluster

### etcd

- etcd is a key value pair data in which all the data required to managet the different nodes in a cluster is stored.
- It enable logs to the nodes to disable conflicts with the multiple master in a cluster.

### Scheduler

- It is responsible for distributing the work or containers b/w nodes.

### Controllers

- Brain behind the orchestration.
- They are responsible for noticing and responding when nodes get down.
- They build new nodes when some one goes down.

### Container RUN TIME

- The underlying software that used to run the container.
- We monstly use docker

### Kublet

- kublet is the agent that runs in every node.
- The agent is responsible to make node runs as expected.

---

## Kubectl - Commamds

- `kubectl run <name>` - to deploy and manage applications in kubernetes

- `kubectl cluster-info` - to get info of cluster

- `kubectl get nodes` to get nodes

## CLI tools

### nerdctl

- "nerdctl" this is a cli tool for containerD
- this works similar to docker cli just relace docker in the cmd and place nerdctl .

### CRIctl

- tool mantained and eveloped by kubernetes
- it works with container run time capable tools.
- used to inspect and debug containers
