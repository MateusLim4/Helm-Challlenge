# Guia de Testes do Helm Chart / Helm Chart Testing Guide

## Visão Geral / Overview

Este documento descreve como testar o Helm chart incluído neste repositório.

This document describes how to test the Helm chart included in this repository.

## Tipos de Testes / Test Types

### 1. Validação de Sintaxe / Syntax Validation

```bash
# Validar a sintaxe do chart
helm lint myapp

# Verificar se os templates renderizam corretamente
helm template myapp-test myapp
```

### 2. Dry-Run

```bash
# Simular instalação sem aplicar no cluster
helm install myapp-test myapp --dry-run --debug
```

### 3. Testes Automatizados / Automated Tests

O chart inclui três testes automatizados:

The chart includes three automated tests:

#### test-connection.yaml
Testa a conectividade HTTP ao serviço usando wget.

Tests HTTP connectivity to the service using wget.

#### test-service.yaml
Verifica se o serviço pode ser resolvido via DNS.

Verifies that the service can be resolved via DNS.

#### test-deployment.yaml
Valida que o deployment foi criado corretamente.

Validates that the deployment was created correctly.

### Executar Testes em um Cluster / Running Tests in a Cluster

**Pré-requisito:** Você precisa ter um cluster Kubernetes rodando (minikube, kind, ou cluster real).

**Prerequisite:** You need a running Kubernetes cluster (minikube, kind, or real cluster).

```bash
# 1. Instalar o chart
helm install myapp-test myapp

# 2. Verificar se os recursos foram criados
kubectl get all -l app.kubernetes.io/instance=myapp-test

# 3. Executar os testes
helm test myapp-test

# 4. Ver os logs dos testes
kubectl logs -l "helm.sh/hook=test"

# 5. Limpar após os testes
helm uninstall myapp-test
```

## Testes Locais com Minikube / Local Testing with Minikube

```bash
# Iniciar minikube
minikube start

# Instalar o chart
helm install myapp-test myapp

# Executar testes
helm test myapp-test

# Ver os pods de teste
kubectl get pods -l "helm.sh/hook=test"

# Ver logs de um teste específico
kubectl logs myapp-test-test-connection
kubectl logs myapp-test-test-service
kubectl logs myapp-test-test-deployment

# Limpar
helm uninstall myapp-test
minikube stop
```

## Testes com Kind / Testing with Kind

```bash
# Criar cluster kind
kind create cluster --name helm-test

# Instalar o chart
helm install myapp-test myapp

# Executar testes
helm test myapp-test

# Limpar
helm uninstall myapp-test
kind delete cluster --name helm-test
```

## Testando com Valores Customizados / Testing with Custom Values

```bash
# Usar arquivo de valores customizados
helm install myapp-test myapp -f myapp/examples/custom-values.yaml

# Ou passar valores via linha de comando
helm install myapp-test myapp \
  --set replicaCount=3 \
  --set image.tag=1.25-alpine

# Executar testes
helm test myapp-test
```

## Verificação de Resultados / Result Verification

### Teste Bem-Sucedido / Successful Test
```
NAME: myapp-test
LAST DEPLOYED: ...
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE:     myapp-test-test-connection
Last Started:   ...
Last Completed: ...
Phase:          Succeeded
TEST SUITE:     myapp-test-test-service
Last Started:   ...
Last Completed: ...
Phase:          Succeeded
TEST SUITE:     myapp-test-test-deployment
Last Started:   ...
Last Completed: ...
Phase:          Succeeded
```

### Debug de Falhas / Debugging Failures

```bash
# Ver status dos pods de teste
kubectl get pods -l "helm.sh/hook=test"

# Ver logs detalhados
kubectl logs <pod-name> --previous

# Descrever pod para ver eventos
kubectl describe pod <pod-name>

# Manter pods de teste para debug (não deletar automaticamente)
helm test myapp-test --logs
```

## Integração Contínua / Continuous Integration

Exemplo de pipeline CI/CD:

CI/CD pipeline example:

```yaml
test:
  script:
    - helm lint myapp
    - helm template myapp-test myapp
    - helm install myapp-test myapp --wait
    - helm test myapp-test
    - helm uninstall myapp-test
```

## Boas Práticas / Best Practices

1. **Sempre faça lint antes de instalar** / Always lint before installing
   ```bash
   helm lint myapp
   ```

2. **Use dry-run para verificar mudanças** / Use dry-run to verify changes
   ```bash
   helm upgrade myapp-test myapp --dry-run --debug
   ```

3. **Execute testes após cada instalação/upgrade** / Run tests after each install/upgrade
   ```bash
   helm test myapp-test
   ```

4. **Mantenha os testes simples e rápidos** / Keep tests simple and fast

5. **Use valores customizados em ambientes diferentes** / Use custom values for different environments

## Troubleshooting

### Teste falha por timeout
- Aumente os recursos do cluster
- Verifique se as imagens estão acessíveis
- Verifique logs: `kubectl logs <pod-name>`

### Service não encontrado
- Verifique se o service foi criado: `kubectl get svc`
- Verifique labels e selectors
- Use `kubectl describe svc <service-name>`

### Deployment não encontrado
- Verifique se tem permissões RBAC adequadas
- Verifique se o serviceAccount tem permissões: `kubectl get clusterrole`

## Recursos Adicionais / Additional Resources

- [Helm Chart Tests Documentation](https://helm.sh/docs/topics/chart_tests/)
- [Kubernetes Testing Guide](https://kubernetes.io/docs/tasks/debug/)
- [Helm Best Practices](https://helm.sh/docs/chart_best_practices/)
