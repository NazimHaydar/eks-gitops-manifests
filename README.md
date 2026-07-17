# EKS GitOps Manifests

Kubernetes manifests for the `my-devops-app` application, continuously deployed to Amazon EKS by ArgoCD.

## What this repo is

This repository holds **only** the Kubernetes Deployment and Service definitions for the application — it contains no infrastructure code. It exists as a separate repo from the EKS/Terraform infrastructure by design: this is the GitOps convention of keeping "what should run" (this repo) separate from "where it runs" (the [eks-gitops-app](https://github.com/NazimHaydar/eks-gitops-app) infrastructure repo).

ArgoCD watches this repository. Any change pushed to `main` is automatically detected and applied to the cluster — there is no manual deployment step.

## Contents

- **Deployment**: runs the containerized app (image sourced from [cicd-docker-pipeline](https://github.com/NazimHaydar/cicd-docker-pipeline)'s ECR repository)
- **Service**: a LoadBalancer type Service exposing the app on port 80, provisioning a real AWS Elastic Load Balancer

## How a change flows through this repo

```
1. Edit deployment.yaml, git push
2. ArgoCD detects the change (polling or webhook)
3. ArgoCD applies the updated manifest to the cluster
4. Cluster state converges to match Git — automatically
```

## Notes from building this

- Node capacity matters: `t3.small` EKS nodes have a low per-node pod limit, which caused pods to get stuck `Pending` until the node group was scaled up — a good reminder that container count planning is part of real deployment work, not just writing the YAML
- Image tag typos fail silently until the pod actually tries to pull — worth double-checking image references carefully before pushing

## Tech stack

Kubernetes · ArgoCD (GitOps controller) · Amazon EKS

## Related repos

- [eks-gitops-app](https://github.com/NazimHaydar/eks-gitops-app) — Terraform code for the EKS cluster and ArgoCD installation
- [cicd-docker-pipeline](https://github.com/NazimHaydar/cicd-docker-pipeline) — builds and pushes the container image referenced here

## Author

**Nazim Haydar**
IT Support / Network Engineer transitioning into Cloud & DevOps Engineering
[GitHub](https://github.com/NazimHaydar)
