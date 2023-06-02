---
title: "Cleaning up"
teaching: 5
exercises: 10
questions:
- "How do I clean my workspace?"
- "How do I delete my cluster?"
objectives:
- "Clean my workflows"
- "Delete my storage volume"
keypoints:
- "With a couple commands it is easy to get back to square one."
---


## Cleaning workspace

Remember to delete your workflows:
Run this until you get a message indicating there is no more workflows.

```bash
argo delete -n argo @latest
```

## Cleaning resources
Do not forget to _download/delete_ any files created in your `/tmp/poddata/` local directory. In respect to K8s, deleting the argo namespace will delete all the resources created in this pre-exercise:

```bash
kubectl delete ns argo
```
<br/>

> ## Minikube - Stop your cluster
> 
> Before closing Docker Desktop, from a terminal with administrator access (but not logged in as root), run:
> ```bash
> minikube stop
> ```
{: .testimonial}


