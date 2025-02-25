# Lab04 - ArgoCD SyncPolicy e Self-Healing

## 📌 Introdução

Agora vamos aprender, finalmente, como configurar a sincronização automática né?

## ⚙️ Pré-requisitos

- Cluster Kubernetes configurado (ex: Minikube)
- ArgoCD instalado no cluster

## 🛠 Criando a Application

Crie um arquivo `app.yaml` com o seguinte conteúdo:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: web-color
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/Layzan/web-page-deploy.git
    path: k8s
    targetRevision: HEAD
  syncPolicy:
    automated: {}
  destination:
    namespace: web-color
    server: https://kubernetes.default.svc
  project: default
```

## Sincronização Automática

O ArgoCD sincroniza automaticamente a aplicação sem necessidade de intervenção manual. A cada 3 minutos, ele verifica se há mudanças no repositório e aplica as atualizações.

Para garantir que recursos removidos no repositório sejam excluídos no ambiente, adicione:

```yaml
syncPolicy:
    automated:
      prune: true
```

## Self-Healing

Para evitar que mudanças aplicadas diretamente no cluster fiquem diferentes do repositório Git, habilite o **Self-Healing**:

```yaml
syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Resumo das Configurações:

- **Prune**: Remove do ambiente recursos que foram deletados no repositório.
- **Self-Healing**: Restaura automaticamente qualquer mudança feita no ambiente para o estado definido no Git.

Com essas configurações, o ArgoCD mantém o ambiente sempre sincronizado com o repositório, garantindo maior controle e previsibilidade das aplicações no Kubernetes. 🚀

