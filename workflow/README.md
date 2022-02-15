# Setup #

# Create cluster using kind
kind create cluster --config kind.yaml

# Install `argo` from 

kubectl create ns argo

kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.3.0-rc4/install.yaml


kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/stable/manifests/quick-start-minimal.yaml

kubectl -n argo get deploy workflow-controller

# Users typically want to process and store data in a workflow, for the quick start we use MinIO, which is similar to Amazon S3:

kubectl -n argo get deploy minio

# Finally, the Argo Server provides a user interface and API:

kubectl -n argo get deploy argo-server

# Before we proceed, let wait (around 1 minute to 2 minutes) for our deployments to be available:

kubectl -n argo wait deploy --all --for condition=Available --timeout 2m

# What is a workflow?
A workflow is defined as a Kubernetes resource. Each workflow consists of one or more templates, one of which is defined as the entrypoint. Each template can be one of several types, in this example we have one template that is a container.

There are several other types of template, and we'll come to more of them soon.

```
kubectl -n argo apply -f hello-workflow.yaml
```
x
# Then you can wait for it to complete (around 1m):
```
kubectl -n argo wait workflows/hello --for condition=Completed --timeout 2m
```

# Using UI
You can view the user interface by running a port forward:

kubectl -n argo port-forward deployment/argo-server 2746:2746

# The first category defines work to be done. This includes:

Container
Container Set
Data
Resource
Script

# The second category orchestrate the work:

DAG
Steps
Suspend