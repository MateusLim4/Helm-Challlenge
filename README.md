# Helm-Challenge

Projeto para armazenar testes de Helm chart (Project to store Helm chart tests)

## Descrição / Description

Este repositório contém um Helm chart de exemplo com testes automatizados para demonstrar as melhores práticas de testes em Kubernetes.

This repository contains a sample Helm chart with automated tests to demonstrate best practices for testing in Kubernetes.

## Estrutura do Projeto / Project Structure

```
myapp/
├── Chart.yaml              # Metadados do chart
├── values.yaml             # Valores padrão de configuração
├── templates/              # Templates Kubernetes
│   ├── deployment.yaml     # Deployment
│   ├── service.yaml        # Service
│   ├── serviceaccount.yaml # Service Account
│   ├── ingress.yaml        # Ingress (opcional)
│   ├── hpa.yaml            # Horizontal Pod Autoscaler (opcional)
│   ├── httproute.yaml      # HTTPRoute (opcional)
│   └── tests/              # Testes do Helm
│       ├── test-connection.yaml  # Teste de conectividade HTTP
│       ├── test-service.yaml     # Teste de resolução DNS do service
│       └── test-deployment.yaml  # Teste de existência do deployment
└── charts/                 # Dependências do chart
```

## Pré-requisitos / Prerequisites

- Helm 3.x
- Kubernetes cluster (minikube, kind, ou cluster real)
- kubectl configurado

## Como Usar / How to Use

### 1. Validar o Chart / Validate Chart

```bash
helm lint myapp
```

### 2. Instalar o Chart / Install Chart

```bash
# Modo dry-run para verificar os manifests
helm install myapp-test myapp --dry-run --debug

# Instalação real (requer cluster Kubernetes)
helm install myapp-test myapp
```

### 3. Executar Testes / Run Tests

```bash
# Executar todos os testes do chart
helm test myapp-test

# Ver logs dos testes
kubectl logs -l "helm.sh/hook=test"
```

### 4. Atualizar o Chart / Update Chart

```bash
helm upgrade myapp-test myapp
```

### 5. Desinstalar / Uninstall

```bash
helm uninstall myapp-test
```

## Testes Incluídos / Included Tests

1. **test-connection.yaml**: Testa a conectividade HTTP ao serviço usando wget
2. **test-service.yaml**: Verifica a resolução DNS do serviço
3. **test-deployment.yaml**: Valida que o deployment foi criado corretamente

## Personalização / Customization

Você pode personalizar os valores no arquivo `values.yaml` ou passar valores via linha de comando:

You can customize values in the `values.yaml` file or pass values via command line:

```bash
helm install myapp-test myapp --set replicaCount=3
helm install myapp-test myapp --set image.repository=my-custom-image
```

## Desenvolvimento / Development

Para modificar o chart:

1. Edite os templates em `myapp/templates/`
2. Atualize valores padrão em `myapp/values.yaml`
3. Valide as mudanças: `helm lint myapp`
4. Teste em modo dry-run: `helm install test myapp --dry-run`

## Recursos / Resources

- [Helm Documentation](https://helm.sh/docs/)
- [Helm Chart Tests](https://helm.sh/docs/topics/chart_tests/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

## Licença / License

MIT