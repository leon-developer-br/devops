### Tipos de recursos

- Pod
- Clusterrole
- Deployments
- Service
- Custom Resource Definition (CRD)
- ConfigMap
- Secrets
- Role
- ClusterRoleBinding
- ClusterIssuer
- Issuers (certificados para um namespace especifico)

### Pod

### Namespace

### kubectl

### Event

Verificar problemas ocorrendo no cluster. 

kubectl get pods

-n [namespace]

Para ficar em watch passamos o -w.

### Services

Um serviçco é usado para comunicação entre os pods dentro do próprio cluster como também para expor para acesso externo.

QUando uma app em um pod quer acessar uma app em outro pod, a configuracao de IP vai apontar para o service.

```
kubectl get svc
```

> ClusterIP

Expoe um pod internamento dentro do cluster.

> LoadBalancer

Permite acesso aos pods de fora do cluster.

### Conectando ao nó

### Volumes

O Kubernets permite que o nó exponha pontos de montagem para os pods no cluster.
Após o claim, o k8s irá procurar por um volume que satisfaça ao pedido.

### Segredo

Criar um novo segredo
kubectl create secret [tipo][nome]
kubectl create secret generic mysql --from-literal=password=kubernets

Listar os segredos cadastrados
kubectl get secrets

### Addons

- DNS: kube-dns.

### Helm

Gerenciador de pacotes para o K8S. O formato de empacotamento de chama charts. Um aplicativo pode ser empacotado em um chart e o Helm consegue realizar a implantação automatico do aplicativo no cluster.

O nome do pacote é kubernetes-helm.

O Helm é composto do helm client que é uma cli. É necessário criar uma conta para o **_tiller_** que é o Helm dentro do cluster que irá realizar o deploy.

> Criação da conta

```
kubectl - n kube-system create serviceaccount tiller.
```

> Associar permissão para a conta

```
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-ystem:tiller
```

> Iniciar o Helm

```
helm init --service-account tiller.
```

Este comando cria um pod do tiller que irá realizar as implantações no cluster.

> Versão do Helm

```
helm version
```

> Listando deployments

```
helm ls
```

> Remover o Helm

```
helm del --purge [name]
```

> Adicionar repositorio

helm repo add [nome] [link]

> Atualizar os repositórios

helm repo update

### Ingress

O serviço do Ingress depende de um nginx. Funciona como um balanceador de carga na camada de aplicação.

As configurações de roteamento são satisfeitas pelo Ingress Controller.

```
kubectl create namespace nginx-ingress
```

> Implantação via Helm

```
helm install stable/nginx-ingress --namespace nginx-ingress
```

Ingress >> Service >> Pods

```
kubectl get svc -n nginx-ingress
```

É criado um LoadBalancer onde o external ip é o IP do cluster. Através deste IP, o acesso via mundo externo é habilitado.

> Implantação manual

No site do serviço, existe um yaml com todos os recursos a serem criados para subir o Ingress. Depois temos que criar um serviço que será um LoadBalancer baseado na nuvem onde está o cluster.


### Dashboard

Temos um yaml que cria todos os artefatos para subir o serviço.

Para acessar o servico rodamods kubectl proxy. O acesso é via localhost. 

O serviço é instalado no namespace kube-system.

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
  
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespece: kube-system
  
  
  kubectl -n kubesystem describe secret
  
  
  ### Service accounts
  
  kubectl get serviceaccounts
  
  ### Cluster role
  
  sudo microk8s.kubectl get clusterroles
