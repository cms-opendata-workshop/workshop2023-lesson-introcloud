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


> Do not forget to go through the [setup](https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/setup.html) episode to get your environment up and running.
> Also, check out the kubectl [cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).
{: .testimonial}

<div id="kubernetes-run">
  <div>
        <ul class="nav nav-tabs" role="tablist">
        <li role="presentation" class="active"><a data-os="Minikube" href="#shell-minikube" aria-controls="Minikube" role="tab" data-toggle="tab">Minikube</a></li>
        <li role="presentation"><a data-os="Docker" href="#shell-docker" aria-controls="Docker" role="tab" data-toggle="tab">Docker Desktop</a></li>
        </ul>
        <div class="tab-content">
            <article role="tabpanel" class="tab-pane active" id="shell-minikube">
              
<p>Docker desktop needs to run in order to Minikube to start. From a terminal with administrator access (but not logged in as root), run:</p>
<pre><code>minikube start
</code></pre>
              <blockquote class="testimonial">
  <p>If [minikube](https://minikube.sigs.k8s.io/docs/start/) fails to start, see the [drivers page](https://minikube.sigs.k8s.io/docs/drivers/) for help setting up a compatible container or virtual-machine manager.</p>
</blockquote>
              
            </article><!-- Minikube  -->

            <article role="tabpanel" class="tab-pane" id="shell-docker">
              
 To activate Docker Desktop Kubernetes, follow these steps:
<ol>
  <li>Install Docker Desktop: If you haven't already, download and install Docker Desktop for your operating system from the official Docker website. Ensure that you have administrative privileges to install software on your machine.</li>
  <li>Enable Kubernetes: Once Docker Desktop is installed, launch the application. Depending on your operating system, you may find the Docker Desktop icon in your system tray or application launcher. Open the Docker Desktop settings.</li>
  <li>Go to Kubernetes Settings: In the Docker Desktop settings, navigate to the "Kubernetes" tab. Here, you'll find options related to Kubernetes configuration.</li>
  <li>Enable Kubernetes: Enable the Kubernetes checkbox to activate Kubernetes support in Docker Desktop. This will start the process of setting up and enabling Kubernetes on your local machine.</li>
  <li>Wait for Setup: Docker Desktop will initiate the setup process for Kubernetes, which may take a few moments. It will automatically download the required components and set up the Kubernetes cluster on your local machine.</li>
  <li>Verify Kubernetes Status: Once the setup is complete, you can verify the status of Kubernetes by checking the Docker Desktop interface. The status indicator will change to "Running" or "Enabled" to indicate that Kubernetes is active.</li>
  <li>Access Kubernetes Cluster: Docker Desktop automatically configures the <code>kubectl</code> command-line tool to interact with the local Kubernetes cluster. To access the Kubernetes cluster, open a terminal or command prompt and run the following command to verify that <code>kubectl</code> is correctly configured and connected to the local cluster:</li>
</ol>
<pre><code>kubectl version
</code></pre>
<p>Congratulations! You have successfully activated Docker Desktop Kubernetes. You can now use Kubernetes to deploy and manage containerized applications on your local machine. Remember to familiarize yourself with Kubernetes concepts and commands to make the most of its capabilities.</p>
              
              
                            </article><!-- Docker  -->
        </div> <!-- tab-contents  -->
    </div><!-- nav-tabs  -->
</div><!-- kubernetes-run  -->



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
> ## Output
> The status of the pod is "ContainerCreating," which means that Kubernetes is currently in the process of creating the container for the pod. The "0/1" under the "READY" column indicates that the pod has not yet reached a ready state. 
> ~~~
> NAME      READY   STATUS              RESTARTS   AGE
> mynginx   0/1     ContainerCreating   0          5s
> ~~~
> {: .language-output}
> 
> Once the pod is successfully created and the container is running, the status will change to "Running" or a similar value, and the "READY" column will reflect that the pod is ready.
> ~~~
> NAME      READY   STATUS              RESTARTS   AGE
> mynginx   1/1     Running             0          70s
> ~~~
> {: .language-output}
{: .solution}

##### Get more info
```bash
kubectl get pods -o wide
```
> ## Output
> The updated output indicates that the pod named "mynginx" is now successfully running and ready to serve requests.
> ~~~
> NAME      READY   STATUS    RESTARTS   AGE     IP             NODE       NOMINATED NODE   READINESS GATES
> mynginx   1/1     Running   0          4m23s   10.244.0.122   minikube   <none>           <none>
> ~~~
> {: .language-output}
{: .solution}
  
```bash
kubectl describe pod mynginx
```
> ## Output
> The status of the pod is "ContainerCreating," which means that Kubernetes is currently in the process of creating the container for the pod. The "0/1" under the "READY" column indicates that the pod has not yet reached a ready state. 
> ~~~
> Name:             mynginx                                     # Pod name
> Namespace:        default                                     # Namespace of the Pod
> Priority:         0                                           # Priority assigned to the Pod
> Service Account:  default                                     # Service account used by the Pod
> Node:             minikube/192.168.49.2                       # Node where the Pod is running
> Start Time:       Thu, 01 Jun 2023 18:46:23 -0500             # Time when the Pod started
> Labels:           run=mynginx                                 # Labels assigned to the Pod
> Annotations:      <none>                                      # Annotations associated with the Pod
> Status:           Running                                     # Current status of the Pod
> IP:               10.244.0.122                                # IP address assigned to the Pod
> IPs:
>   IP:  10.244.0.122                                           
> Containers:
>   mynginx:
>     Container ID:   docker://c22dce8c953394...               # ID of the container
>     Image:          nginx                                    # Image used by the container
>     Image ID:       docker-pullable://nginx@sha256:af296b... # ID of the container image
>     Port:           <none>                                   # Port configuration for the container
>     Host Port:      <none>                                   # Host port configuration for the container
>     State:          Running                                  # Current state of the container
>       Started:      Thu, 01 Jun 2023 18:46:50 -0500          # Time when the container started
>     Ready:          True                                     # Indicates if the container is ready
>     Restart Count:  0                                        # Number of times the container has been restarted
>     Environment:    <none>                                   # Environment variables set for the container
>     Mounts:
>       /var/run/secrets/kubernetes.io/serviceaccount from...  # Mount points for the container
> Conditions:
>   Type              Status                                    # Various conditions related to the Pod's status
>   Initialized       True                                      # Pod has been initialized
>   Ready             True                                      # Pod is ready
>   ContainersReady   True                                      # Containers are ready
>   PodScheduled      True                                      # Pod has been scheduled
> Volumes:
>   kube-api-access-hvg8b:                                      # Volumes used by the Pod
>     Type:                    Projected
>     TokenExpirationSeconds:  3607
>     ConfigMapName:           kube-root-ca.crt
>     ConfigMapOptional:       <nil>
>     DownwardAPI:             true
> QoS Class:                   BestEffort                         # Quality of service class for the Pod
> Node-Selectors:              <none>                             # Node selectors for the Pod
> Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
>                              node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
> Events:
>   Type    Reason     Age    From               Message         # Events related to the Pod
>   ----    ------     ----   ----               -------
>   Normal  Scheduled  7m32s  default-scheduler  Successfully assigned default/mynginx to minikube
>   Normal  Pulling    7m32s  kubelet            Pulling image "nginx"
>   Normal  Pulled     7m5s   kubelet            Successfully pulled image "nginx" in 26.486269096s (26.486276637s including waiting)
>   Normal  Created    7m5s   kubelet            Created container mynginx
>   Normal  Started    7m5s   kubelet            Started container mynginx
> ~~~
> {: .language-output}
{: .solution}

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
> ## Output
> We used the command to execute an interactive shell session within the "myapp-pod" pod. After executing this command, you are now inside the pod and running commands as the "root" user. The prompt "root@myapp-pod:/#" indicates that you are currently in the pod's shell environment and can run commands.
> ~~~
> root@myapp-pod:/#
> ~~~
> {: .language-output}
{: .solution}

Print the DBCON environment variable that was set in the YAML file.
```bash
echo $DBCON
```
> ## Output
> The command "echo $DBCON" is used to print the value of the environment variable "DBCON".
> ~~~
> root@myapp-pod:/# echo $DBCON
> myconnectionstring
> ~~~
> {: .language-output}
{: .solution}

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
kubectl get pods -n kube-system
```
  
> ## Output
> The command "kubectl get pods --namespace=kube-system" is used to retrieve information about the pods running in the "kube-system" namespace. 
> ~~~
> NAME                               READY   STATUS    RESTARTS        AGE
> coredns-787d4945fb-62wpc           1/1     Running   9 (4d19h ago)   34d
> etcd-minikube                      1/1     Running   8 (4d19h ago)   34d
> kube-apiserver-minikube            1/1     Running   9 (4d19h ago)   34d
> kube-controller-manager-minikube   1/1     Running   9 (4d19h ago)   34d
> kube-proxy-hm78n                   1/1     Running   8 (4d19h ago)   34d
> kube-scheduler-minikube            1/1     Running   8 (4d19h ago)   34d
> storage-provisioner                1/1     Running   16 (18m ago)    34d
> ~~~
> {: .language-output}
{: .solution}

#### Get nodes information:
Get a list of all the installed nodes. Using Docker Desktop or Minikube, there should be only one.
```bash
kubectl get nodes
```

Get some info about the node.
```bash
kubectl describe node
```
> ## Output
> The output provides details about each node, including its name, status, roles, age, and version.
> ~~~
> NAME       STATUS   ROLES           AGE   VERSION
> minikube   Ready    control-plane   34d   v1.26.3
> ~~~
> {: .language-output}
{: .solution}

### Run your first deployment
A [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) is a higher-level resource that provides declarative updates and manages the deployment of Pods. It allows you to define the desired state of your application, including the number of replicas, container images, and resource requirements.
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
> ## Output
> Deployments are Kubernetes resources that manage the lifecycle of replicated pods, ensuring high availability, scalability, and automated healing, while ReplicaSets are used by deployments to maintain a desired number of pod replicas, enabling rolling updates and fault tolerance.
> ~~~
> NAME                             READY   STATUS              RESTARTS   AGE   IP       NODE       NOMINATED NODE   READINESS GATES
> deploy-example-54fbc9897-56s28   0/1     ContainerCreating   0          4s    <none>   minikube   <none>           <none>
> deploy-example-54fbc9897-9twh8   0/1     ContainerCreating   0          4s    <none>   minikube   <none>           <none>
> deploy-example-54fbc9897-lng78   0/1     ContainerCreating   0          4s    <none>   minikube   <none>           <none>
> ~~~
> {: .language-output}
> 
> All three pods are currently in the "ContainerCreating" state, indicating that the containers are being created for these pods. After some time you will get:
> ~~~
> NAME                             READY   STATUS    RESTARTS   AGE   IP             NODE       NOMINATED NODE   READINESS GATES
> deploy-example-54fbc9897-56s28   1/1     Running   0          42s   10.244.0.126   minikube   <none>           <none>
> deploy-example-54fbc9897-9twh8   1/1     Running   0          42s   10.244.0.124   minikube   <none>           <none>
> deploy-example-54fbc9897-lng78   1/1     Running   0          42s   10.244.0.125   minikube   <none>           <none>
> ~~~
> {: .language-output}
{: .solution}

Get more details about the pod
```bash
kubectl describe pod deploy-example
```
  
> ## Output
> The output shows three running pods named "deploy-example-54fbc9897-56s28", "deploy-example-54fbc9897-9twh8", and "deploy-example-54fbc9897-lng78" in the "default" namespace. These pods are controlled by the ReplicaSet "deploy-example-54fbc9897" and are running the "nginx:alpine" image. They have successfully pulled the image, created the container, and started running. Each pod has an IP assigned, and they are running on the "minikube" node.
{: .solution}
  
Get the Deployment info
```bash
kubectl get deploy
```
> ## Output
> All 3 replicas are ready, up-to-date, and available, indicating that the deployment is successfully running. 
> ~~~
> NAME             READY   UP-TO-DATE   AVAILABLE   AGE
> deploy-example   3/3     3            3           4m57s
> ~~~
> {: .language-output}
{: .solution}

#### Get the ReplicaSet name:
A ReplicaSet is a lower-level resource that ensures a specified number of replicas of a Pod are running at all times.
```bash
kubectl get rs
```
> ## Output
>  The current replica count matches the desired count, indicating that the ReplicaSet has successfully created and maintained 3 replicas. All 3 replicas are ready and available. 
> ~~~
> NAME                       DESIRED   CURRENT   READY   AGE
> deploy-example-54fbc9897   3         3         3       6m12s
> ~~~
> {: .language-output}
{: .solution}

In summary, a Deployment provides a higher-level abstraction for managing and updating the desired state of Pods, while a ReplicaSet is a lower-level resource that ensures the specified number of Pod replicas are maintained. Deployments use ReplicaSets under the hood to achieve the desired state and handle scaling and rolling updates.

#### Cleanup
Delete the pod
```bash
kubectl delete -f deploy-example.yaml
```


{% include links.md %}
