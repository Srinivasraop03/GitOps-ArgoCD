# GitOps Argo CD Bootstrap Repository

This repository is the source of truth for the GitOps platform. It manages the installation of Argo CD, configurations, and the deployment of microservices via ApplicationSets.

## Architecture

- **bootstrap/**: Contains the Kustomize base to install Argo CD itself on the cluster (Bootstrapping).
- **projects/**: Defines `AppProject` CRDs which establish guardrails (destinations, source repos, resource allow-lists) for your applications.
- **applicationsets/**: Contains `ApplicationSet` definitions. This is the automation engine that generates Argo CD `Application` resources for your microservices.

## Microservices Strategy

This setup assumes 3 independent microservice repositories. The `ApplicationSet` in `applicationsets/` is configured to track these repositories (or a list of them) and sync them automatically.

1.  **CI Process**: Builds image -> Tags (Immutable) -> Updates Helm/Kustomize in Service Repo.
2.  **CD Process**: Argo CD detects change -> Syncs (Prune + Self Heal).

## Usage

1.  **Bootstrap**: Apply the bootstrap folder to install Argo CD.
    ```bash
    kubectl apply -k bootstrap/
    ```
2.  **Apply Config**: Once Argo CD is running, apply the projects and applicationsets.
    ```bash
    kubectl apply -f projects/
    kubectl apply -f applicationsets/
    ```
