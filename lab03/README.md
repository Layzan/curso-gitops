# Laboratório 03 - Autogerenciamento do ArgoCD e Gerenciamento de Aplicações

## 📌 Introdução
Neste laboratório, iremos configurar o **ArgoCD** para se autogerenciar e também gerenciar outras aplicações no cluster Kubernetes.

## 🛠 Passo 1: Instalação Manual do ArgoCD

Primeiro, criamos um arquivo chamado **`install.yaml`**, contendo a configuração oficial do ArgoCD, conforme a documentação.

Link: https://github.com/argoproj/argo-cd/blob/master/manifests/install.yaml

Aplicamos esse arquivo no cluster para instalar o ArgoCD:

```sh
kubectl apply -f install.yaml
```

Isso garante que o ArgoCD seja implantado e possa começar a operar dentro do cluster.

## 🛠 Passo 2: Organização do Repositório

Criamos uma pasta chamada **`applications`** e movemos para lá as aplicações configuradas nos laboratórios anteriores:

- **app-prometheus.yaml**
- **app-webcolor.yaml**

Em seguida, subimos esse projeto para o **GitHub**, garantindo que o ArgoCD possa acessá-lo futuramente.

## 🛠 Passo 3: Configuração do Autogerenciamento

Criamos um novo arquivo chamado **`app-argocd.yaml`**, que define a aplicação do próprio ArgoCD para se autogerenciar:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: argocd
  namespace: argocd
spec:
  project: default
  sources:
    - repoURL: https://github.com/Layzan/curso-gitops.git
      path: lab03
      targetRevision: HEAD
      # directory:
      #   recurse: true
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
```

> **Observação:** No campo `repoURL`, substitua pelo repositório correto onde o projeto foi armazenado.

Aplicamos o arquivo para criar a aplicação no ArgoCD:

```sh
kubectl apply -f app-argocd.yaml
```

Neste momento, o ArgoCD já está se autogerenciando, ou seja, ele monitorará e aplicará qualquer alteração feita nesse repositório automaticamente.

## 🛠 Passo 4: Gerenciamento de Outras Aplicações

Para que o ArgoCD também gerencie as aplicações dentro da pasta **`applications`**, basta descomentar a seção `directory` no arquivo **`app-argocd.yaml`**, ficando assim:

```yaml
sources:
  - repoURL: https://github.com/Layzan/curso-gitops.git
    path: lab03
    targetRevision: HEAD
    directory:
      recurse: true
```

Depois, reaplicamos o arquivo:

```sh
kubectl apply -f app-argocd.yaml
```

Agora, o ArgoCD não apenas se autogerencia, mas também gerencia todas as aplicações definidas dentro da pasta **`applications`**, garantindo que estejam sempre implantadas corretamente no cluster.

## Conclusão

Com essa configuração, automatizamos totalmente o gerenciamento do ArgoCD e das demais aplicações, tornando o processo de implantação mais eficiente e confiável. 🚀

