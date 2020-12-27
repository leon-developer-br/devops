### Regra de roteamento

> Manifesto 

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: echo-ingres
spec:
  rules:
  - host: echo1.apps.com.br
    http:
      paths:
      - backend:
        serviceName: echo1
        servicePort: 80
  - host: echo2.apps.com.br
    http:
      paths:
      - backend:
        serviceName: echo2
        servicePort: 80
  
> Configuração

spec.rules.host é o dominio que vai apontar para um IP que é o IP do LoadBalancer criado.
serviceName: indica que a entrada via echo1.apps.com.br deve ser roteada para o servico echo1.

### Cert Manager


> Introdução

Este serviço permite instalar e gerenciar certificados SSL no cluster. Ele fornece certificados do Lets Encrypt ou outras CAs para o cluster. A instalação pode ser via Helm ou manual.

> Emissor (issuer)

É necessário configurar uma autoridade de certificação.

apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: your_email_address_here
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
    
A url https://acme-staging-v02.api.letsencrypt.org/directory é de staing. A url https://acme-v02.api.letsencrypt.org/directory é de produção. 

O privateKeySecretRef é um secret do K8S que vai guardar a chave privada da conta do acme.

kubectl appy -f ./issuer.yaml.

kubectl get clusterissuer

### SSL + Ingress

Annotations é um conjunto de pares chave valor para inserir metadados para os objetos do K8s.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: echo-ingres
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt
spec:
  tls: 
  - hosts:
    - echo1.apps.com.br
    - echo2.apps.com.br
    secretName: letsencrypt-staging
  rules:
  - host: echo1.apps.com.br
    http:
      paths:
      - backend:
        serviceName: echo1
        servicePort: 80
  - host: echo2.apps.com.br
    http:
      paths:
      - backend:
        serviceName: echo2
        servicePort: 80
```

Em metadata:

```     
+ annotations:
  + kubernetes.io/ingress.class: nginx
  + certmanager.k8s.io/cluster-issuer: letsencrypt
```

Em spec:

```
+ tls: 
  + - hosts:
  + - echo1.apps.com.br
  + - echo2.apps.com.br
  + secretName: letsencrypt-staging
```
  
  > Listar os certificados 
  
  ```
  kubectl describe certificate
  ```
  
  ### Outras annotations do Ingress
  
  É possivel usar outras diretivas proprias de um nginx através de ConfigMap na seção annotations
  
  nginx.ingress.kubernetes.io/proxy-body-size: 8m
  nginx.ingress.kubernetes.io/affinity: cookie
  
  Podemos gerar um arquivo auth com usuario e senha user:password para o .htpassword. Depois vamos criar um secret.
  
  kubectl create secret generic basic-auth --from-file=auth
  
  kubectl get secret basic-auth
  
  nginx.ingress.kubernetes.io/auth-type: basic
  nginx.ingress.kubernetes.io/auth-secret: basic-auth
  nginx.ingress.kubernetes.io/auth-realm: "Autenticacao necessária"
  
  kubectl apply -f ./ingress-rules.yaml.