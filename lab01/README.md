# Lab01: Instalação do ArgoCD e Prometheus com Helm

## 📌 Objetivo
Este laboratório tem como objetivo instalar o ArgoCD e o Prometheus em um cluster Kubernetes utilizando Helm.

## ⚙️ Pré-requisitos
Para realizar este laboratório, é necessário:
- Ter acesso a um cluster Kubernetes (neste exemplo, utilizamos o Minikube);
- Ter o Helm instalado;
- Ter o `kubectl` configurado para se conectar ao cluster.

## 🛠 Passo 1: Instalação do ArgoCD com Helm
Criamos um arquivo `values.yaml` com a seguinte configuração:

```yaml
redis-ha:
  enabled: false

controller:
  replicas: 1

server:
  replicas: 2

repoServer:
  replicas: 2

applicationSet:
  replicas: 2
```

Executamos o comando abaixo para instalar o ArgoCD:

```sh
helm upgrade argo-cd argo/argo-cd \
  --namespace argocd \
  --create-namespace \
  --install \
  --version 7.8.2 \
  --values values.yaml
```

## 🛠 Passo 2: Instalação do Prometheus
Criamos um arquivo `app-prometheus.yaml` com o seguinte conteúdo:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: prometheus
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://prometheus-community.github.io/helm-charts
    chart: prometheus
    targetRevision: 27.3.1
    helm:
      releaseName: prometheus
  destination:
    namespace: monitoramento
    server: https://kubernetes.default.svc
```

Aplicamos o arquivo com o seguinte comando:

```sh
kubectl apply -f app-prometheus.yaml
```

## ✅ Conclusão
Com esses passos, instalamos o ArgoCD e configuramos o Prometheus como uma aplicação gerenciada pelo ArgoCD no Kubernetes. Agora, podemos acompanhar a implantação e sincronização dos recursos pelo painel do ArgoCD. 🚀

