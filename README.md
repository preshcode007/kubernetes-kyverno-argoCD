![project diagram](./assets/Untitled%20Diagram-Page-2.drawio.png)
# The AntAuthority Project

## This project automates Kubernetes Cluster Security using  Kyverno Policy Generator and ArgoCD. 
The project was implemented on AWS Cloud and could be implemented on other Cloud Service Providers as well.


## High Level Design

On a very high level, I wrote the required Kyverno Policy custom resource and committed it to a Git repository. ArgoCD which is pre configured with `auto-sync` watches for resources in the git repo and then deploys the Kyverno Policies on to the Kubernetes cluster.


## Installation

To setup this project you need to install ArgoCD controller and Kyverno controller, Assuming you have Kubernetes installed.

Installation of both Kyverno and Argo CD are pretty straight forward as both of them support Helm charts and also provide a consolidated 
installation yaml files. 

### Kyverno

There are two easy ways to install kyverno:

1. Using Helm
2. Using the kubernetes manifest files

### Using Helm 

Add helm repo for kyverno 

```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
```

Install kyverno in HA mode

```
 helm install kyverno kyverno/kyverno -n kyverno --create-namespace --set replicaCount=3
```

(or)

Install kyverno in Standalone mode

```
helm install kyverno kyverno/kyverno -n kyverno --create-namespace --set replicaCount=1
```

Install a specific version of kyverno

```
helm search repo kyverno -l | head -n 10
```

```
helm install kyverno kyverno/kyverno -n kyverno --create-namespace --version 2.6.5
```

### Using Kubernetes manifest yaml files

```
kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.8.5/install.yaml
```

### Argo CD

There are three ways to install Argo CD

1. `kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/master/manifests/install.yaml`
2. Helm Charts, Follow the [link](https://github.com/argoproj/argo-helm/tree/main/charts/argo-cd#installing-the-chart) 
3. Using the Argo CD Operator, Follow the [link](https://argocd-operator.readthedocs.io/en/latest/install/olm/)

## Demystifying Kyverno & Kyverno Policies 

Kyverno is a policy engine designed for Kubernetes

A Kyverno policy is a collection of rules. Each rule consists of a match declaration, an optional exclude declaration, and one of a validate, mutate, generate, or verifyImages declaration. Each rule can contain only a single validate, mutate, generate, or verifyImages child declaration.

![image](https://user-images.githubusercontent.com/43399466/219931973-14c0f501-ae49-4cab-9da5-b01950cc308f.png)

Policies can be defined as cluster-wide resources (using the kind ClusterPolicy) or namespaced resources (using the kind Policy.) As expected, namespaced policies will only apply to resources within the namespace in which they are defined while cluster-wide policies are applied to matching resources across all namespaces. Otherwise, there is no difference between the two types.

Additional policy types include PolicyException and (Cluster)CleanupPolicy which are separate resources and described further in the documentation.

### Architecture

![image](https://user-images.githubusercontent.com/43399466/219931795-dce93e3b-9f78-42ef-ba5e-9aa685252e2f.png)
