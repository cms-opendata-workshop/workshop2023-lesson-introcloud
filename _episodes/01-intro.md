---
title: "Introduction"
teaching: 10
exercises: 0
questions:
- "What is Kubernetes?"
- "What is a Kubernetes cluster and why do I need one?"
objectives:
- "Learn the very basics of Kubernetes"
- "Learn a bit about the architecture of a Kubernetes cluster"
keypoints:
- "Kubernetes is an orchestrator of containers. It is most useful when it is run in a cluster of computers."
- "Commercial K8s clusters are a good option for large computing needs."
- "We can run our containerized CMSSW jobs and subsequent analysis workflows in a K8s cluster."
---

## Introduction:
Throughout this workshop, you have become familiar with Docker containers and their ability to function as isolated, efficient virtual machines running on a host machine. Considering this, imagine the potential of maximizing hardware resources by running multiple CMSSW (Compact Muon Solenoid Software) open data containers on a single desktop. For example, running 10 CMSSW containers, each processing a single root file, skimming through entire datasets, in parallel, simultaneously, and on your own machine. Scaling up to a larger number of machines introduces new challenges. _How would you manage the software installation across all these machines? Do you have sufficient resources to handle these tasks? How would you effectively manage and monitor all the containers running across the distributed infrastructure?_

These questions highlight the need for a robust orchestration system like Kubernetes. By leveraging Kubernetes, you can streamline and automate the deployment, scaling, and management of containers across multiple machines. Kubernetes provides a unified platform to address these challenges and ensures efficient utilization of computing resources, enabling researchers to focus on their analysis tasks rather than infrastructure management.

In the upcoming sections of this workshop, we will delve into the practical aspects of using Kubernetes for managing CMSSW containers and orchestrating data processing workflows. We will explore techniques for software deployment, container management, and effective utilization of distributed resources. By the end of the workshop, you will have gained the knowledge and skills to leverage Kubernetes for efficient and scalable physics data analysis.

## Kubernetes (K8s) - Microservices Concepts
Kubernetes is a powerful container orchestration platform that facilitates the deployment, scaling, and management of microservices-based applications. Microservices architecture is an approach to developing software applications as a collection of small, independent services that work together to form a larger application. Kubernetes provides essential features and functionality to support the deployment and management of microservices.

<img src="https://imgopt.infoq.com/fit-in/1200x2400/filters:quality(80)/filters:no_upscale()/articles/distributed-systems-kubernetes/en/resources/13image006-1616431699209.jpg" width="500">

### K8s API
The Kubernetes API (Application Programming Interface) is a set of rules and protocols that allows users and external systems to interact with a Kubernetes cluster. It serves as the primary interface for managing and controlling various aspects of the cluster, including deploying applications, managing resources, and monitoring the cluster's state. Users can interact with the API using various methods, such as command-line tools (e.g., **kubectl**), programming languages (e.g., Python, Go), or through user interfaces built on top of the API.

<img src="https://collabnix.com/wp-content/uploads/2022/01/Screen-Shot-2022-01-22-at-10.52.54-AM.png" width="400">

## Kubernetes Components
When deploying Kubernetes, you establish a cluster that comprises two main components: masters and workers.  

* **Master Nodes:**
Kubernetes masters are responsible for managing the control plane of the cluster. They coordinate and orchestrate the activities of the worker nodes and ensure the proper functioning of the entire cluster. 

* **Worker Nodes:**
Worker nodes are the machines within the Kubernetes cluster that execute the actual application workloads. They host and run the Pods, which are the fundamental units of deployment in Kubernetes. Each Pod consists of one or more containers that work together to provide the desired functionality.

By separating the responsibilities of the masters and workers, Kubernetes ensures a distributed and scalable architecture. The masters focus on managing the cluster's control plane and coordinating the overall state, while the workers handle the execution of application workloads. This division of labor allows for efficient scaling, fault tolerance, and high availability in a Kubernetes cluster.

<img src="https://spaceliftio.wpcomstaging.com/wp-content/uploads/2022/12/kubernetes-architecture-diagram.png" width="600">

### Nodes Components
Kubernetes nodes, also referred to as **worker nodes** or simply nodes, are the individual machines or virtual machines that make up a Kubernetes cluster. These nodes are responsible for executing the actual workloads and running the containers that make up your applications. Each node in a Kubernetes cluster plays a crucial role in the distributed system and contributes to the overall functioning of the cluster. Here are the key characteristics and components of Kubernetes nodes:

* **Pods:**
In the Kubernetes world, pods are the smallest computing units. A pod represents a group of one or more containers that are deployed together on a node. Pods are fundamental to understanding Kubernetes architecture. However, when working with services, we typically work with a higher-level abstraction called deployments. Deployments automatically manage the creation and scaling of pods on our behalf.

* **Containers:**
Containers are lightweight and isolated environments that package applications and their dependencies, providing consistency and portability across different environments. In the context of pods, containers are the individual units that encapsulate the application components. These containers are typically extracted from **Docker images**, which are portable and self-contained units that include the application code, runtime, system tools, and libraries required to run the application.

* **Kubelet:**
The kubelet is the primary "node agent" that runs on each node in the Kubernetes cluster.  It interacts with the cluster's control plane to ensure that the containers described in the PodSpecs (pod specifications) are running and healthy.

* **Kube-Proxy:**
Kube-proxy is responsible for network proxying and load balancing within the Kubernetes cluster.

* **Container Runtime:**
The container runtime is responsible for managing the low-level operations of the containers, including image management, container creation, networking, and storage.

<img src="https://res.cloudinary.com/escalante-rep/image/upload/v1589159144/i14yfj2jn5nm70bzekxu.jpg" width="600">

Nodes form the backbone of a Kubernetes cluster, offering the necessary computational resources for running applications. Working in collaboration with the master components, nodes play a crucial role in _orchestrating, scheduling, and managing_ the lifecycle of containers and pods. By hosting and executing pods, nodes effectively utilize their compute resources, ensuring optimal execution, resource allocation, and scalability. With Kubernetes' intelligent scheduling capabilities, containers are seamlessly distributed across nodes, enabling efficient resource utilization and facilitating fault tolerance in a distributed environment.

## Autoscaling
Autoscaling is a powerful feature supported by Kubernetes that allows you to optimize the allocation of resources on your nodes based on the actual usage patterns of your applications. Kubernetes enables you to automatically scale up or down the number of nodes in your cluster, as well as adjust the CPU and memory resources allocated to those nodes.

By utilizing autoscaling, you can ensure that your applications have the necessary resources to handle increased workloads during peak times, while also dynamically reducing resource allocation during periods of lower demand. This flexibility not only improves performance and responsiveness but also helps optimize costs by allowing you to pay only for the resources you actually need. If you want to learn about pricing for this workshop's cloud provider, check out Google's [Compute Engine pricing](https://cloud.google.com/compute/all-pricing).
