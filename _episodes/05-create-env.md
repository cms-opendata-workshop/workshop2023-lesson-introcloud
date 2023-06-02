---
title: "Storing a workflow output on Kubernetes"
teaching: 5
exercises: 30
questions:
- "How to setup a workflow engine to submit jobs?"
- "How to run a simple job?"
- "How can I set up shared storage for my workflows?"
- "How to run a simple job and get the the ouput?"
objectives:
- "Understand how to run a simple workflows in a commercial cloud environment or local machine"
- "Understand how to set up shared storage and use it in a workflow"
keypoints:
- "With Kubernetes one can run workflows similar to a batch system"
- "Open Data workflows can be run in a commercial cloud environment using modern tools"
---


## Kubernetes Cluster - Storage Volume

When running applications or workflows, it is often necessary to allocate disk space to store the resulting data. Kubernetes provides various options for managing storage volumes, allowing you to efficiently store and access data within your cluster. Local storage volumes are tied to the Minikube host machine and are not accessible outside of it since they are meant for local development and testing purposes. By leveraging local storage volumes with Minikube, you can conveniently create and utilize storage resources within your local Kubernetes cluster, enabling data persistence and local development capabilities.

Let's create a persistent volume, retrieve the persistent volume configuration file with:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/minikube/pv.yaml
```

It has the following content, you can alter the storage capacity if you'd like to whatever value.
```yaml
# pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/mnt/vol"
```
Deploy:
```bash
kubectl apply -f pv.yaml
```
Check:
```bash
kubectl get pv
```
Apps can claim persistent volumes through persistent volume claims (pvc). Let’s create a pvc, retrieve the pvc with:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/minikube/pvc.yaml
```
It has the following content, you can alter the storage request if you'd like, but it mas less or equal than the storage capacity defined in our persistent volume (previous step).
```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```
Deploy:
```bash
kubectl apply -f pvc.yaml -n argo
```
Check:
```bash
kubectl get pvc -n argo
```
Now an argo workflow can claim and access this volume, retrieve the configuration file with:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/minikube/argo-wf-volume.yaml
```

It has the following content:
```yaml
# argo-wf-volume.yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: test-hostpath-
spec:
  entrypoint: test-hostpath
  volumes:
    - name: workdir
      hostPath:
        path: /mnt/vol
        type: DirectoryOrCreate
  templates:
  - name: test-hostpath
    script:
      image: alpine:latest
      command: [sh]
      source: |
        echo "This is the new ouput" > /mnt/vol/test1.txt
        echo ls -l /mnt/vol: `ls -l /mnt/vol`
      volumeMounts:
      - name: workdir
        mountPath: /mnt/vol
```

Submit and check this workflow with:

```bash
argo submit argo-wf-volume.yaml -n argo
```
Wait till the pod `test-hostpath-XXXXX` is created, you can check with:
```bash
kubectl get pods -A -n argo
```
List all the workflows with:
```bash
argo list -n argo
```
Take the name of the workflow from the output (replace XXXXX in the following command) and check the logs:

```bash
kubectl logs pod/test-hostpath-XXXXX  -n argo main
```

Once the job is done, you will see something like:

```output
time="2022-07-25T05:51:14.221Z" level=info msg="capturing logs" argo=true
ls -l /mnt/vol: total 4 -rw-rw-rw- 1 root root 18 Jul 25 05:51 test.txt
```
## Get the output file

The example job above produced a text file as an output. It resides in the persistent volume that the workflow job has created. To copy the file from that volume to the shell, we will define a container, a “storage pod”, and mount the volume there so that we can get access to it.

Retrieve the file pv-pod.yaml with:
```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/minikube/pv-pod.yaml
```

It has the following content:
```yaml
# pv-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: busybox
      command: ["tail", "-f", "/dev/null"]
      volumeMounts:
        - mountPath: /mnt/vol
          name: task-pv-storage
      resources:
        limits:
          cpu: "2"
          memory: "3Gi"
        requests:
          cpu: "1"
          memory: "512Mi"
---
```

Create the storage pod and copy the files from there with:
```bash
kubectl apply -f pv-pod.yaml -n argo
```
Wait till the pod `task-pv-pod` is created, you can check with:
```bash
kubectl get pods -A -n argo
```
Now copy the files into your machine with:
```bash
kubectl cp task-pv-pod:/mnt/data /tmp/poddata -n argo
```
and you will get the file created by the job in /tmp/poddata/test.txt.

> Every time you want the files to get copied from your the pv-pod to your local computer you must run `kubectl cp task-pv-pod:/mnt/vol /tmp/poddata -n argo`.
{:testimonial}
