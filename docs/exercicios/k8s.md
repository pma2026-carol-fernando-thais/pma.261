# Kubernetes

## Objetivo

Orquestrar todos os microsserviços em um único cluster Kubernetes na AWS (EKS), garantindo escalabilidade, alta disponibilidade e gerenciamento centralizado.

## Cluster

| Item | Valor |
|---|---|
| Provedor | AWS EKS |
| Região | `us-east-2` |
| Nome | `eks-store2` |
| Namespace | `store` |

## Recursos por Serviço

Cada serviço possui `k8s/k8s.yaml` com:

| Recurso | Função |
|---|---|
| **Secret** | Credenciais sensíveis (senhas, chave JWT) — jamais hardcoded |
| **ConfigMap** | Configurações não-sensíveis (host do banco, porta, CORS) |
| **Deployment** | Imagem Docker, réplicas, variáveis de ambiente, resource limits |
| **Service** | DNS interno no cluster (`ClusterIP`) ou entrada externa (`LoadBalancer`) |

## Serviços Implantados

| Serviço | Service name | Tipo | Réplicas |
|---|---|---|---|
| postgres | `postgres` | ClusterIP | 1 |
| account-service | `account` | ClusterIP | 1 |
| auth-service | `auth` | ClusterIP | 1 |
| gateway-service | `gateway` | LoadBalancer | 3 |
| product-service | `product-service` | ClusterIP | 1 |
| order-service | `order-service` | ClusterIP | 2 |

## Deploy

```bash
# 1. Criar namespace
kubectl create namespace store

# 2. Banco de dados
kubectl apply -f api/postgres-service/k8s/

# 3. Serviços (com variáveis do .env)
export $(grep -v '^#' api/.env | xargs)
envsubst < api/account-service/k8s/k8s.yaml | kubectl apply -f -
envsubst < api/auth-service/k8s/k8s.yaml    | kubectl apply -f -
kubectl apply -f api/gateway-service/k8s/k8s.yaml
envsubst < api/product-service/k8s/k8s.yaml | kubectl apply -f -
```

## HPA — Escalabilidade Automática do Gateway

```yaml
minReplicas: 1
maxReplicas: 10
targetCPUUtilizationPercentage: 50
```

O gateway escala automaticamente quando a CPU ultrapassa 50%. Ver [Teste de Carga](../projeto/teste-carga.md).

## Verificação

```bash
kubectl get pods     -n store
kubectl get services -n store
kubectl get hpa      -n store
```
