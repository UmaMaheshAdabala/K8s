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
- It also acts as a backup

### Scheduler

- It is responsible for distributing the work or containers b/w nodes.
- Basically the API Server decides the action and the Scheduler performs the action.

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

# Abishek - K8s

## Why K8s and why not docker containers

- Let say there are 100 containers running on a docker host but the 2nd container in consuming more cpu and memory. so that contaner x and y and not getting enough resources and they got died. so to avoid this we use K8s.

- Lack of Auto Healing in Docker.

- Lack of Auto Scaling in Docker.

## compare with ECS

- | **Kubernetes**     | **Amazon ECS**             | 📝 Explanation                              |
  | ------------------ | -------------------------- | ------------------------------------------- |
  | `Cluster`          | ECS Cluster                | Group of compute instances                  |
  | `Node`             | EC2 instance / Fargate     | Worker machine running containers           |
  | `Pod`              | Task                       | Unit of deployment (can run 1+ containers)  |
  | `Container`        | Container                  | Actual running app inside a pod/task        |
  | `Deployment`       | ECS Service                | Manages lifecycle, scaling, rolling updates |
  | `ReplicaSet`       | ECS Desired Task Count     | Ensures required number of tasks/pods       |
  | `Service` (K8s)    | ECS Service + Target Group | Exposes pod/task and manages networking     |
  | `ConfigMap/Secret` | ECS Env Vars / Secrets     | External configuration management           |
  | `Ingress`          | ALB/NLB                    | Expose apps to internet users               |
  | `kube-scheduler`   | ECS Scheduler              | Chooses where to place pods/tasks           |
  | `kubelet`          | ECS agent                  | Node-level controller for pods/tasks        |

---

## K8s in Production Env

- Basically in general purpose env they use minicube and kubectl
- But in production they use `kops` to install

### Steps

### Install dependencies

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt-get update
sudo apt-get install -y python3-pip apt-transport-https kubectl
```

```
pip3 install awscli --upgrade
```

```
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### Install KOPS (our hero for today)

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Provide the below permissions to your IAM user. If you are using the admin user, the below permissions are available by default

1. AmazonEC2FullAccess
2. AmazonS3FullAccess
3. IAMFullAccess
4. AmazonVPCFullAccess

### Set up AWS CLI configuration on your EC2 Instance or Laptop.

Run `aws configure`

## Kubernetes Cluster Installation

Please follow the steps carefully and read each command before executing.

### Create S3 bucket for storing the KOPS objects.

```
aws s3api create-bucket --bucket kops-abhi-storage --region us-east-1
```

### Create the cluster

```
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-abhi-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro  --master-volume-size=8 --node-volume-size=8
```

---
