# CI/CD

## Ferramenta

Jenkins — cada microsserviço possui seu próprio `Jenkinsfile` e existe um Jenkinsfile raiz em `api/Jenkinsfile` que orquestra todos os serviços.

## Stages

| # | Stage | Descrição |
|---|---|---|
| 1 | **SCM** | Checkout do repositório (`checkout scm`) |
| 2 | **Dependencies** | Build da biblioteca de contrato do serviço |
| 3 | **Build** | `mvn -B -DskipTests clean package` |
| 4 | **Vulnerability Scan** | OWASP Dependency Check — falha se CVSS ≥ 11 |
| 5 | **Build & Push Image** | Imagem multi-plataforma (linux/arm64 + linux/amd64) publicada no Docker Hub |
| 6 | **Deploy to K8s** | `aws eks update-kubeconfig` + `kubectl apply` no cluster `eks-store2` |

## Orquestração

O `api/Jenkinsfile` raiz dispara todos os serviços em sequência:

```
account-service → auth-service → gateway-service → product-service → order-service
```

## Cluster

| Variável | Valor |
|---|---|
| `AWS_REGION` | `us-east-2` |
| `EKS_CLUSTER` | `eks-store2` |

## Credenciais

Todas as credenciais são gerenciadas pelo Jenkins (`withCredentials`) — nenhum valor sensível aparece no código:

| ID | Uso |
|---|---|
| `dockerhub-credential` | Push de imagens |
| `db-credential` | Banco de dados (envsubst) |
| `aws-credentials` | Acesso ao EKS |
| `jwt-secret-key` | Chave JWT do auth-service |
