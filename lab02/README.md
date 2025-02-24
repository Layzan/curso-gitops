# Lab02 - Publicação de Aplicação com Múltiplas Fontes

## 📌  Introdução
Neste laboratório, iremos publicar nossa aplicação utilizando duas fontes diferentes:
1. Um repositório Helm, que contém o chart do Prometheus. Link: https://prometheus-community.github.io/helm-charts
2. Um repositório Git, onde estão armazenados os arquivos `values.yaml` para configurar a instalação do Prometheus. Link: https://github.com/Layzan/web-page-deploy.git

## ⚙️ Pré-requisitos
Para realizar este laboratório, é necessário ter acesso a um cluster Kubernetes. Neste exemplo, utilizamos o **Minikube**.

## 🛠 Configuração do ArgoCD
Criaremos um recurso `Application` no ArgoCD para gerenciar a instalação do Prometheus a partir das duas fontes mencionadas.

### Arquivo `app-prometheus.yaml`
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: prometheus
  namespace: argocd
spec:
  project: default
  sources:
    - repoURL: https://prometheus-community.github.io/helm-charts
      chart: prometheus
      targetRevision: 27.3.1
      helm:
        releaseName: prometheus
        valuesFiles:
          - $values/prometheus/values.yaml
    - repoURL: https://github.com/Layzan/web-page-deploy.git
      targetRevision: HEAD
      ref: values
  destination:
    namespace: monitoramento
    server: https://kubernetes.default.svc
```

## Aplicando a Configuração
Para aplicar a configuração, utilizamos o seguinte comando:

```sh
kubectl apply -f app-prometheus.yaml
```

## Conclusão
Após a aplicação do arquivo, o ArgoCD irá sincronizar os recursos utilizando as configurações do repositório Helm para instalar o Prometheus, e do repositório Git para aplicar os arquivos `values.yaml`. Isso permite uma abordagem mais flexível e modular para implantações Kubernetes.

