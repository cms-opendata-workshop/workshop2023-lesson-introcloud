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

Deleting the argo namespace will delete all the resources created in this pre-exercise:

```bash
kubectl delete ns argo
```

> ## Minikube - Stop your cluster
> 
> From a terminal with administrator access (but not logged in as root), run:
> ```bash
> minikube stop
> ```
{: .testimonial}

Perfect! You’re ready to [start over](https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/03-basics-kubectl/index.html).
