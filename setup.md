---
title: Setup
---
## Welcome!
The workshop will introduce participants to Kubernetes, an open-source container orchestration platform. Kubernetes provides a scalable and efficient infrastructure for running applications in the cloud. It allows users to manage and automate the deployment, scaling, and monitoring of containerized applications. In the context of the CMS OpenData workshop, Kubernetes will be used to create an environment on [Google Cloud Platform](https://cloud.google.com), enabling participants to run realistic physics analyses in the cloud.

## Setup
In this pre-exercise, we will guide you through setting up a K8s cluster on your local computer in order to learn and test workflows. This will give participants a broad understanding of the functionality of Argo workflows, thus preparing them to run realistic physics analysis using Google Cloud Platform (GCP) in the workshop itself. Here are the steps to get started:

### 1. GCP (Google Cloud Platform):
To participate in this workshop, you will need a [GCP](https://cloud.google.com) account (you can use your existing google account to access this). Activating a GCP account allows you to access $300.00 USD in credit, which can be used within a period of 3 months. Please note that a credit or debit card is required during the account creation process. 
<br/>

> However, it's important to mention that for the Cloud Computing lesson, we will provide temporary resources, so activating your own GCP account credit is not entirely necessary. If you do wish to activate your credits, we recommend you do so after the workshop. More detailed instructions will follow for the workshop resources!
{: .testimonial}

### 2. Minikube:
For this Cloud Pre-exercise, you will need to create a Kubernetes cluster locally to run some tests. Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. Minikube is available for Linux, macOS, and Windows systems. Please click [here](https://minikube.sigs.k8s.io/docs/start/) to get started with the minikube installation. 

>  To enable Kubernetes on WSL2, you have two options: activating Kubernetes in Docker Desktop or installing Minikube following the Linux option on WSL2 Ubuntu. It’s important to note that the Windows instructions for the Minikube installation guide users to PowerShell, but running the CMSSW container there will cause issues. Therefore, it is necessary to execute those commands within the Ubuntu shell.
{: .testimonial}

### 3. Mattermost Cloud Pre-Exercises Channel:
Don't forget to subscribe to the [Mattermost cloud pre-exercises channel](https://mattermost.web.cern.ch/cmsodws2023/channels/cloud-pre-exercise). This channel will provide you with important updates, announcements, and any additional resources or support you may need throughout the workshop. It's a valuable communication platform to stay connected with instructors and fellow participants.

We wish you good luck with the workshop! Feel free to reach out if you have any questions or need assistance along the way. Enjoy the learning experience!

{% include links.md %}
