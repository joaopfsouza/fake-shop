# Fake Shop


## Variável de Ambiente
DB_HOST	=> Host do banco de dados PostgreSQL.

DB_USER => Nome do usuário do banco de dados PostgreSQL.

DB_PASSWORD	=> Senha do usuário do banco de dados PostgreSQL.

DB_NAME	=>	Nome do banco de dados PostgreSQL.

DB_PORT	=>	Porta de conexão com o banco de dados PostgreSQL.

## K8S

```shell
kubectl api-resources

kubectl rollout undo deployment fakeshop && watch 'kubectl get po'

kubectl rollout history deployment fakeshop
```

## K3D

```shell
k3d cluster create --servers 3 --agents 3

k3d cluster create jaxstar --servers 3 --agents 3 -p "5000:30000@loadbalancer"

````

- [K3D](https://k3d.io/v5.6.3/)

## Github Action


Workflow usado

```yaml
name: FakeShop
run-name: "FakeShop ${{ github.run_number }}"

# Triggers
on: 
  push:
    branches:
      - 'main'
  workflow_dispatch:

jobs:
 CI:
  # Define runner type
  runs-on: ubuntu-latest
  # Define Environment Variables
  environment: dev
  steps:

  # Git  Clone
  - uses: actions/checkout@v4

  # Docker Login
  - uses: docker/login-action@v3
    with:
      username: ${{ vars.DOCKERHUB_USERNAME  }}
      password: ${{ secrets.DOCKERHUB_TOKEN }}

  # Build and Push Image to DockerHub
  - name: Build and push
    uses: docker/build-push-action@v6
    with:
      push: true
      context: ${{ github.workspace}}/src
      tags: |
        ${{ vars.DOCKERHUB_USERNAME  }}/${{ vars.DOCKER_IMAGE}}:v${{ github.run_number }}
        ${{ vars.DOCKERHUB_USERNAME  }}/${{ vars.DOCKER_IMAGE}}

 CD:
  runs-on: ubuntu-latest
  needs: [CI]
  environment: dev
  steps:
    
  # Git  Clone
  - uses: actions/checkout@v4

  # Set kubeconfig K8S DigitalOcean
  - uses: azure/k8s-set-context@v4
    with:
       method: kubeconfig
       kubeconfig: ${{ secrets.KUBERNETES_CONFIG }}

  # Change image manifest and deploy in K8S
  - uses: Azure/k8s-deploy@v5
    with:
      manifests: |
        k8s/deployment.yml
      images: ${{ vars.DOCKERHUB_USERNAME  }}/${{ vars.DOCKER_IMAGE}}:v${{ github.run_number }}

````