# Kargo Playground

## Services:

| Port  | Service              |
|-------|----------------------|
| 31080 | ArgoCD               |
| 31081 | Kargo                |
| 32080 | Web-app (Staging)    |
| 32081 | Web-app (Prod-ring1) |
| 32082 | Web-app (Prod-ring2) |

## Promotions

- Staging: **Automatically**, as soon as a freight is created from the source
- Prod-ring1: **Automatically**, but only after a **soak time**.
- Prod-ring2: After **soak time**, a **PR is opened** for human review and approval. Once the PR is merged, the promotions continues.

## Deployment

1. Prerequisites:
    - docker
    - kind
    - helm
    - kubectl
    - personal fork of this repo

1. Run this command to spin up the demo cluster with Kargo and ArgoCD pre-configured:

    ```bash
    curl -L https://raw.githubusercontent.com/akuity/kargo/main/hack/quickstart/kind.sh | sh
    ```

1. Clone the playground files:

    ```bash
    git clone https://github.com/<your github username>/kargo-playground.git
    ```

1. Deploy the ArgoCD and Kargo CRDs:

    ```bash
    cd kargo-playground/
    kubectl apply -f argocd/
    kubectl apply -f kargo/
    ```

1. Add Github secret:

    ```bash
    export GITOPS_REPO_URL=https://github.com/<your github username>/kargo-playground
    export GITHUB_USERNAME=<your github username>
    export GITHUB_PAT=<your personal access token>

    cat <<EOF | kubectl apply -f -
    apiVersion: v1
    kind: Secret
    type: Opaque
    metadata:
        name: kargo-playground-repo
        namespace: kargo-playground
        labels:
            kargo.akuity.io/cred-type: git
    stringData:
        repoURL: ${GITOPS_REPO_URL}
        username: ${GITHUB_USERNAME}
        password: ${GITHUB_PAT}
    EOF
    ```

1. Deploy Kargo stages:

    ```bash
    kubectl apply -f kargo/stages/
    ```

## Clean-up

```bash
kind delete cluster --name kargo-quickstart
```
