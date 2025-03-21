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

kubectl rollout undo deployment fakeshop && watch 'kubectl get po'                                <az:TFTECOLP>
deployment.apps/fakeshop rolled back

kubectl rollout history deployment fakeshop
```

## K3D

```shell
k3d cluster create --servers 3 --agents 3

k3d cluster create jaxstar --servers 3 --agents 3 -p "5000:30000@loadbalancer"

````

- [K3D](https://k3d.io/v5.6.3/)