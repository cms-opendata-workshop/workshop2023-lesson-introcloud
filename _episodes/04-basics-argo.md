---
title: "Getting started with Argo"
teaching: 10
exercises: 0
questions:
- "What is Argo?"
- "How to use Argo commands?"
- "What are Argo workflows?"
- "How do I deploy my Argo GUI?"

objectives:
- "Appreciate the necessity for the Argo workflows tool (or similar)"
- "Learn the basics of Argo commands"

keypoints:
- "Argo is a very useful tool for running workflows and parallel jobs"
---

## Argo

Argo is a collection of open-source tools that extend the functionality of Kubernetes, providing several benefits for workflow management. Some key advantages of using Argo include:

* **Cloud Agnostic:** Argo can be deployed on any Kubernetes cluster, irrespective of the underlying cloud provider.
* **Resource Monitoring:** Argo enables constant monitoring of resource states, allowing users to track the progress and status of their workflows.
* **Scalability:** Argo supports the execution of multiple jobs simultaneously across different nodes, providing high scalability.
* **Error Debugging:** With Argo, it is easier to identify and debug errors in workflows, ensuring smooth execution.

In the context of Argo, there are three important tools that facilitate working with workflows, we will be using the Argo Workflow Engine.

### Argo Workflow Engine
The Argo Workflow Engine is designed to execute complex job orchestration, including both serial and parallel execution of stages, with each stage executed as a container.

In the context of scientific analysis, such as physics analysis using datasets from the CMS Open Data portal and CMSSW, Argo's orchestration capabilities are particularly valuable. By leveraging Argo, researchers can automate and streamline complex analysis workflows, which often involve multiple processing stages and dependencies. Argo's support for parallel execution and container-based environments allows for efficient utilization of computational resources, enabling faster and more scalable data analysis.

### Install argo as a workflow engine

While jobs can be run manually, utilizing a workflow engine like Argo simplifies the process of defining and submitting jobs. In this tutorial, we will use the Argo Quick Start page to install and configure Argo in your working environment.

Install it into your working environment with the following commands (all commands to be entered into your local shell):

```yaml
kubectl create ns argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/master/manifests/quick-start-postgres.yaml
```

### Patch argo-server authentication

The argo-server (and thus the UI) defaults to client authentication, which requires clients to provide their Kubernetes bearer token in order to authenticate. For more information, refer to the [Argo Server Auth Mode documentation](argo-server-auth-mode.md). We will switch the authentication mode to `server` so that we can bypass the UI login for now:

```bash
kubectl patch deployment \
  argo-server \
  --namespace argo \
  --type='json' \
  -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/args", "value": [
  "server",
  "--auth-mode=server"
]}]'
```

You can now check that argo is available with:
              
```bash
argo version
```    
### Port-forward the UI

Open a port-forward so you can access the UI:

```bash
kubectl -n argo port-forward deployment/argo-server 2746:2746
```

This will serve the UI on [https://localhost:2746](https://localhost:2746). Due to the self-signed certificate, you will receive a TLS error which you will need to manually approve.

> Pay close attention to the URI. It uses `https` and not `http`. Navigating to `http://localhost:2746` result in server-side error that breaks the port-forwarding.
{:testimonial}

## Install the Argo Workflows CLI

Next, Download the latest Argo CLI from the same [releases page](https://github.com/argoproj/argo-workflows/releases/latest).

         
### Run a simple test workflow
             
To test the setup, run a simple test workflow with:
```bash
argo submit -n argo https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
```
This might take a while, you can get the logs with:
```bash
argo logs -n argo @latest
```
              
If argo was installed correctly you will have the following:
              
```output
hello-world-j8dq9: time="2023-05-28T03:34:28.279Z" level=info msg="capturing logs" argo=true
hello-world-j8dq9:  _____________
hello-world-j8dq9: < hello world >
hello-world-j8dq9:  -------------
hello-world-j8dq9:     \
hello-world-j8dq9:      \
hello-world-j8dq9:       \
hello-world-j8dq9:                     ##        .
hello-world-j8dq9:               ## ## ##       ==
hello-world-j8dq9:            ## ## ## ##      ===
hello-world-j8dq9:        /""""""""""""""""___/ ===
hello-world-j8dq9:   ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
hello-world-j8dq9:        \______ o          __/
hello-world-j8dq9:         \    \        __/
hello-world-j8dq9:           \____\______/
hello-world-j8dq9: time="2023-05-28T03:34:29.285Z" level=info msg="sub-process exited" argo=true error="<nil>"
```
              
Please mind that it is important to delete your workflows once they have completed. If you do not do this, the pods associated with the workflow will remain scheduled in the cluster, which might lead to additional charges. You will learn how to automatically remove them later.
              
```bash
argo delete -n argo @latest
```
      
> ## Kubernetes namespaces
>
> The above commands as well as most of the following use a flag `-n argo`,
> which defines the namespace in which the resources are queried or created.
> [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
> separate resources in the cluster, effectively giving you multiple virtual
> clusters within a cluster.
>
> You can change the default namespace to `argo` as follows:
>
> ```bash
> kubectl config set-context --current --namespace=argo
> ```
>
{: .testimonial}

{% include links.md %}
