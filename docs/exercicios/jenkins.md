# Jenkins

## Objetivo

Automatizar build, scan de vulnerabilidades, publicação e deploy de todos os microsserviços no cluster EKS.

## Pipeline por Serviço

Cada microsserviço possui seu próprio `Jenkinsfile` com os seguintes stages:

| # | Stage | O que faz |
|---|---|---|
| 1 | **SCM** | Checkout do código (`checkout scm`) |
| 2 | **Dependencies** | Compila a biblioteca de contrato do serviço |
| 3 | **Build** | Empacota o JAR (`mvn -B -DskipTests clean package`) |
| 4 | **Vulnerability Scan** | OWASP Dependency Check — falha se CVSS ≥ 11 |
| 5 | **Build & Push Image** | Imagem multi-plataforma (arm64/amd64) publicada no Docker Hub |
| 6 | **Deploy to K8s** | Aplica manifesto k8s no EKS via `kubectl apply` |

## Jenkinsfile Raiz

O arquivo `api/Jenkinsfile` orquestra todos os serviços em sequência:

```groovy
pipeline {
    agent any
    stages {
        stage('account-service') { steps { build job: 'account-service', wait: true } }
        stage('auth-service')    { steps { build job: 'auth-service',    wait: true } }
        stage('gateway-service') { steps { build job: 'gateway-service', wait: true } }
        stage('product-service') { steps { build job: 'product-service', wait: true } }
        stage('order-service')   { steps { build job: 'order-service',   wait: true } }
    }
}
```

## Configuração AWS

| Variável | Valor |
|---|---|
| `AWS_REGION` | `us-east-2` |
| `EKS_CLUSTER` | `eks-store2` |

## Credenciais Jenkins

| ID | Tipo | Uso |
|---|---|---|
| `dockerhub-credential` | Username/Password | Login no Docker Hub |
| `db-credential` | Username/Password | `DB_USER` / `DB_PASSWORD` via envsubst |
| `aws-credentials` | AWS Credentials | Acesso ao EKS |
| `jwt-secret-key` | Secret Text | `JWT_SECRET_KEY` para auth-service |

## Segurança

Nenhum valor sensível está no código. Credenciais são injetadas via `withCredentials` e substituídas nos manifests via `envsubst` antes do `kubectl apply`.
