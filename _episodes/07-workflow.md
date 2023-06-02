---
title: "Create an Argo Workflow"
teaching: 5
exercises: 20
questions:
- "How can I visualize my workflows?"
- "How do I deploy my Argo GUI?"
objectives:
- "Prepare to deploy the fileserver that mounts the storage volume."
- "Submit your workflow and get the results."
keypoints:
- "With a simple but a tight yaml structure, a full-blown analysis can be performed using the Argo tool on a K8s cluster."
---

## Workflow Definition

In this section, we will explore the structure and components of an Argo Workflow. Workflows in Argo are defined using YAML syntax and consist of various tasks that can be executed sequentially, in parallel, or conditionally.

To define a workflow, create a YAML file (e.g., `my-workflow.yaml`) and define the following:

- Metadata: Provide a name and optional labels for the workflow.
- Spec: Define the workflow's specification, including the list of tasks to be executed.

Here's an example of a simple Argo Workflow definition, get it with:

```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/argo/container-workflow.yaml
```

The container template will have the following content:

> YAML File
> ~~~
> apiVersion: argoproj.io/v1alpha1
> kind: Workflow
> metadata:
>   name: my-workflow
> spec:
>   entrypoint: my-task
>   templates:
>     - name: my-task
>       container:
>         image: my-image
>         command: [echo, "Hello, Argo!"]
> ~~~
> {: .language-yaml}
{: .solution}
        
Let's run the workflow:
```
argo submit container-workflow.yaml -n argo
```
> You can add the `--watch` flag to supervise the creation of the workflow in real time as so:
> ~~~
> argo submit --watch container-workflow.yaml -n argo
> ~~~
> {: .language-bash}
{: .testimonial}

Open the Argo Workflows UI. Then navigate to the workflow, you should see a single container running.

> ## Exercise
>
> Edit the workflow to make it echo "howdy world".
>
> > ## Solution
> > ~~~
> > apiVersion: argoproj.io/v1alpha1
> > kind: Workflow                 
> > metadata:
> >   generateName: container-   
> > spec:
> >   entrypoint: main         
> >   templates:
> >   - name: main             
> >     container:
> >       image: docker/whalesay
> >       command: [cowsay]         
> >       args: ["howdy world"]
> > ~~~
> > {: .language-yaml}
> {: .solution}
{: .challenge}

## DAG Template

A **DAG template** is a common type of _orchestration_template. Let's look at a complete example:

```
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/argo/dag-workflow.yaml
```

That has the content:

> YAML File
> ~~~
> apiVersion: argoproj.io/v1alpha1
> kind: Workflow
> metadata:
>   generateName: dag-
> spec:
>   entrypoint: main
>   templates:
>     - name: main
>       dag:
>         tasks:
>           - name: a
>             template: whalesay
>           - name: b
>             template: whalesay
>             dependencies:
>               - a
>     - name: whalesay
>       container:
>         image: docker/whalesay
>         command: [ cowsay ]
>         args: [ "hello world" ]
> ~~~
> {: .language-yaml}
{: .solution}

In this example, we have two templates:

-   The "main" template is our new DAG.
-   The "whalesay" template is the same template as in the container example.

The DAG has two tasks: "a" and "b". Both run the "whalesay" template, but as "b" depends on "a", it won't start until " a" has completed successfully.

Let's run the workflow:

```bash
argo submit --watch dag-workflow.yaml
```

You should see something like:

```
STEP          TEMPLATE  PODNAME              DURATION  MESSAGE
 ✔ dag-shxn5  main                                       
 ├─✔ a        whalesay       dag-shxn5-289972251  6s          
 └─✔ b        whalesay       dag-shxn5-306749870  6s          
```

Did you see how `b` did not start until `a` had completed?

Open the Argo Server tab and navigate to the workflow, you should see two containers.

> ## Exercise
>
> Add a new task named "c" to the DAG. Make it depend on both "a" and "b". Go to the UI and view your updated workflow graph.
>
> > ## Solution
> > ~~~
> > apiVersion: argoproj.io/v1alpha1
> > kind: Workflow
> > metadata:
> >   generateName: dag-
> > spec:
> >   entrypoint: main
> >   templates:
> >     - name: main
> >       dag:
> >         tasks:
> >           - name: a
> >             template: whalesay
> >           - name: b
> >             template: whalesay
> >             dependencies:
> >               - a
> >           - name: c
> > 	          template: whalesay
> > 	          dependencies:
> >               - a
> > 			        - b
> >     - name: whalesay
> >       container:
> >         image: docker/whalesay
> >         command: [ cowsay ]
> >         args: [ "hello world" ]
> > ~~~
> > {: .language-yaml}
> > And the expected output is:
> > ~~~
> > STEP          TEMPLATE  PODNAME                        DURATION  MESSAGE
> > ✔ dag-hl6lc  main                                                 
> > ├─✔ a        whalesay  dag-hl6lc-whalesay-1306143144  10s         
> > ├─✔ b        whalesay  dag-hl6lc-whalesay-1356476001  10s         
> > └─✔ c        whalesay  dag-hl6lc-whalesay-1339698382  9s       
> > ~~~
> > {: .language-output}   
> {: .solution}
{: .challenge}

## Input Parameters

Let's have a look at an example:

```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/argo/input-parameters-workflow.yaml
```

See the content:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: input-parameters-
spec:
  entrypoint: main
  arguments:
    parameters:
      - name: message
        value: hello world
  templates:
    - name: main
      inputs:
        parameters:
          - name: message
      container:
        image: docker/whalesay
        command: [ cowsay ]
        args: [ "{{inputs.parameters.message}}" ]
```

This template declares that it has one input parameter named "message". See how the workflow itself has arguments?

Run it:

```bash
argo submit --watch input-parameters-workflow.yaml
```

You should see:

```
STEP                       TEMPLATE  PODNAME                 DURATION  MESSAGE
 ✔ input-parameters-mvtcw  main      input-parameters-mvtcw  8s          
```

If a workflow has parameters, you can change the parameters using `-p` using the CLI:

```bash
argo submit --watch input-parameters-workflow.yaml -p message='Welcome to Argo!'
```

You should see:

```
STEP                       TEMPLATE  PODNAME                 DURATION  MESSAGE
 ✔ input-parameters-lwkdx  main      input-parameters-lwkdx  5s          
```

Let's check the output in the logs:

```bash
argo logs @latest
```

You should see:

```python
 ______________
< Welcome to Argo! >
 --------------
    \
     \
      \     
                    ##        .            
              ## ## ##       ==            
           ## ## ## ##      ===            
       /""""""""""""""""___/ ===        
  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
       \______ o          __/            
        \    \        __/             
          \____\______/   
```

## Output Parameters

Output parameters can be from a few places, but typically the most versatile is from a file. In this example, the container creates a file with a message in it:

```yaml
  - name: whalesay
    container:
      image: docker/whalesay
      command: [sh, -c]
      args: ["echo -n hello world > /tmp/hello_world.txt"]
    outputs:
      parameters:
      - name: hello-param		
        valueFrom:
          path: /tmp/hello_world.txt
```

In a DAG template and steps template, you can reference the output from one task, as the input to another task using a **template tag**:

```yaml
      dag:
        tasks:
          - name: generate-parameter
            template: whalesay
          - name: consume-parameter
            template: print-message
            dependencies:
              - generate-parameter
            arguments:
              parameters:
                - name: message
                  value: "{{tasks.generate-parameter.outputs.parameters.hello-param}}"
```

Get the complete workflow:

```bash
wget https://cms-opendata-workshop.github.io/workshop2023-lesson-introcloud/files/argo/parameters-workflow.yaml
```

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: parameters-
spec:
  entrypoint: main
  templates:
    - name: main
      dag:
        tasks:
          - name: generate-parameter
            template: whalesay
          - name: consume-parameter
            template: print-message
            dependencies:
              - generate-parameter
            arguments:
              parameters:
                - name: message
                  value: "{{tasks.generate-parameter.outputs.parameters.hello-param}}"

    - name: whalesay
      container:
        image: docker/whalesay
        command: [ sh, -c ]
        args: [ "echo -n hello world > /tmp/hello_world.txt" ]
      outputs:
        parameters:
          - name: hello-param
            valueFrom:
              path: /tmp/hello_world.txt

    - name: print-message
      inputs:
        parameters:
          - name: message
      container:
        image: docker/whalesay
        command: [ cowsay ]
        args: [ "{{inputs.parameters.message}}" ]
```

Run it:

```bash
argo submit --watch parameters-workflow.yaml
```

You should see:

```php
STEP                     TEMPLATE       PODNAME                      DURATION  MESSAGE
 ✔ parameters-vjvwg      main                                                    
 ├─✔ generate-parameter  whalesay       parameters-vjvwg-4019940555  43s         
 └─✔ consume-parameter   print-message  parameters-vjvwg-1497618270  8s          
```

Learn more about parameters in the Argo Workflows documentation:

-   [Parameters overview](https://argoproj.github.io/argo-workflows/walk-through/parameters/)
-   [Workflow input parameters](https://argoproj.github.io/argo-workflows/workflow-inputs/).

## Conclusion

Congratulations! You have completed the Argo Workflows tutorial, where you learned how to define and execute workflows using Argo. You explored workflow definitions, dag templates, input and output parameters, and monitoring.

Argo Workflows offers a wide range of features and capabilities for managing complex workflows in Kubernetes. Continue to explore its documentation and experiment with more advanced workflow scenarios.

Happy workflow orchestrating with Argo!
