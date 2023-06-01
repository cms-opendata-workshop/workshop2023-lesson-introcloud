---
title: "Getting started with Kubectl"
teaching: 10
exercises: 0
questions:
- "What is Kubectl?"
- "How to use Kubectl commands?"

objectives:
- "Learn what the kubectl command can do"
- "Learn how to set up different services/resources to get the most of your cluster"

keypoints:
- "`kubectl` is the ruler of GKE"
---

## K8s - Imperative vs Declarative programming
In the context of Kubernetes, imperative and declarative are two different paradigms used to define and manage the desired state of resources within a cluster. While imperative commands are useful for ad-hoc tasks or interactive exploration, the declarative approach is more suitable for managing and maintaining resources in a Kubernetes cluster efficiently. Let's explore each approach! But first, we need a tool to interact with our cluster.

<img src="https://ucarecdn.com/024d46aa-b898-4713-b3a7-2c095c94b00e/" width="400">

### Kubectl
The [kubectl](https://kubernetes.io/docs/tasks/tools/) command-line tool is a powerful utility provided by Kubernetes that allows you to interact with and manage Kubernetes clusters. Use the following syntax to run kubectl commands from your terminal window:

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

Where:
* **command:** Specifies the operation you want to perform on one or more Kubernetes resources. Some commonly used commands include `create`, `get`, `describe`, `delete`, `apply`, and `scale`. Each command has its own set of options and subcommands.
* **TYPE:** Indicates the type of Kubernetes resource you want to interact with. It can be a single resource type like `Pod, Deployment, Service`, or a more general term like `all`, `nodes`, `namespaces`, etc.
* **NAME:** Specifies the name of the specific resource you want to operate on.
* **flags:** These are optional flags that modify the behavior of the command. Flags can be used to specify additional parameters, control output formats, apply labels, set resource limits, etc. Flags are specific to each command and can be listed by running `kubectl [command] --help`.
> Do not forget to go through the [setup](https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/setup.html) episode to get your environment up and running...
> Also, check out the kubectl [cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).
{: .testimonial}

> ## Minikube - Start your cluster
> 
> From a terminal with administrator access (but not logged in as root), run:
> ```bash
> minikube start
> ```
> If [minikube](https://minikube.sigs.k8s.io/docs/start/) fails to start, see the [drivers page](https://minikube.sigs.k8s.io/docs/drivers/) for help setting up a compatible container or virtual-machine manager.
{: .testimonial}


### Imperative Approach
In the imperative approach, you specify the exact sequence of commands or actions to be performed to create or modify Kubernetes resources. You interact with the Kubernetes API by issuing explicit instructions.

Let’s first create a node running Nginx by using the imperative way.

##### Create the pod using the Imperative way
```bash
kubectl run mynginx --image=nginx
```

##### Get a list of pods and their status
```bash
kubectl get pods
```

##### Get more info
```bash
kubectl get pods -o wide
kubectl describe pod mynginx
```

##### Delete the pod
```bash
kubectl delete pod mynginx
```

### Declarative Approach
In the declarative approach, you define the desired state of Kubernetes resources in a declarative configuration file (e.g., **YAML** or JSON). Rather than specifying the steps to achieve that state, you describe the desired outcome and let Kubernetes handle the internal details.

##### Create a pod using the declarative way
Download the file:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/kubectl/myapp.yaml
```

> ## YAML File
> This YAML file describes a Pod with an nginx web server container that listens on port 80 and has an environment variable set. 
> The specific behavior and functionality of the nginx web server will depend on the configuration of the nginx image used. 
> ~~~
> # myapp.yaml
> apiVersion: v1
> kind: Pod
> metadata:
>   name: myapp-pod
> spec:
>   containers:
>   - name: nginx-container
>     image: nginx
>     ports:
>     - containerPort: 80
>     env:
>     - name: DBCON
>       value: myconnectionstring
> ~~~
> {: .language-yaml}
{: .solution}

Now, let’s create a pod using the YAML file
```bash
kubectl create -f myapp.yaml
```

Get some info
```bash
kubectl get pods -o wide
kubectl describe pod myapp-pod
```

Open a shell in the running pod
```bash
kubectl exec -it myapp-pod -- bash
```

Print the DBCON environment variable that was set in the YAML file.
```bash
echo $DBCON
```

Exit from the container
```bash
exit
```

Delete the pod
```bash
kubectl delete -f myapp.yaml
```

The declarative approach is the recommended way to manage resources in Kubernetes. It promotes consistency, reproducibility, and automation. You can easily version control the configuration files, track changes over time, and collaborate with team members more effectively.

## Let's run a few examples.

[Kubernetes namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) partition resources in a cluster, creating isolated virtual clusters. They allow multiple teams or applications to coexist while maintaining separation and preventing conflicts. 
#### Get the currently configured namespaces:
```bash
kubectl get namespaces
kubectl get ns
```
Both commands are equivalent and will retrieve the list of namespaces in your Kubernetes cluster. 

#### Get the pods list:
Get a list of all the installed pods.
```bash
kubectl get pods
```
You get the pods from the default namespace. Try getting the pods from the docker namespace. You will get a different list.
```bash
kubectl get pods --namespace=kube-system
kubectl get pods -n kube-system
```

#### Get nodes information:
Get a list of all the installed nodes. Using Docker Desktop or Minikube, there should be only one.
```bash
kubectl get nodes
```

Get some info about the node.
```bash
kubectl describe node
```

### Run your first deployment
A Deployment is a higher-level resource that provides declarative updates and manages the deployment of Pods. It allows you to define the desired state of your application, including the number of replicas, container images, and resource requirements.
<br/>
Download the file:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/kubectl/deploy-example.yaml
```

> ## YAML File
> This Deployment will create and manage three replicas of an nginx container based on the nginx:alpine image. 
> The Pods will have resource requests and limits defined, and the container will expose port 80. 
> The Deployment ensures that the desired state of the replicas is maintained, managing scaling and updating as needed.
> ~~~
> # deploy-example.yaml
> apiVersion: apps/v1
> kind: Deployment
> metadata:
>   name: deploy-example
> spec:
>   replicas: 3
>   revisionHistoryLimit: 3
>   selector:
>     matchLabels:
>       app: nginx
>       env: prod
>   template:
>     metadata:
>       labels:
>         app: nginx
>         env: prod
>     spec:
>       containers:
>       - name: nginx
>         image: nginx:alpine
>         resources:
>           requests:
>             cpu: 100m
>             memory: 128Mi
>           limits:
>             cpu: 250m
>             memory: 256Mi        
>         ports:
>         - containerPort: 80
> ~~~
> {: .language-yaml}
{: .solution}


#### Create the Deployment:
```bash
kubectl apply -f deploy-example.yaml
```

Get the pods list
```bash
kubectl get pods -o wide
```    

Get more details about the pod
```bash
kubectl describe pod deploy-example
```

Get the Deployment info
```bash
kubectl get deploy
kubectl describe deploy deploy-example
```

#### Get the ReplicaSet name:
A ReplicaSet is a lower-level resource that ensures a specified number of replicas of a Pod are running at all times.
```bash
kubectl get rs
```

Describe the ReplicaSet
```bash
kubectl describe rs
```

In summary, a Deployment provides a higher-level abstraction for managing and updating the desired state of Pods, while a ReplicaSet is a lower-level resource that ensures the specified number of Pod replicas are maintained. Deployments use ReplicaSets under the hood to achieve the desired state and handle scaling and rolling updates.

#### Cleanup
Delete the pod
```bash
kubectl delete -f deploy-example.yaml
```


{% include links.md %}
