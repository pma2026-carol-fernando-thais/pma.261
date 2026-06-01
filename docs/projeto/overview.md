# Overview

## Objetivo

Plataforma de e-commerce baseada em microsserviços desenvolvida na disciplina PMA.261, aplicando conceitos de:

- Microsserviços e contratos de API
- Containerização com Docker
- Orquestração com Kubernetes (AWS EKS)
- Integração Contínua e Entrega Contínua (Jenkins)
- Observabilidade (Prometheus + Grafana)
- Segurança (JWT, OWASP, envsubst)
- Cloud Computing (AWS)

## Tecnologias

### Backend

| Serviço | Linguagem | Framework |
|---|---|---|
| account, account-service | Java 25 | Spring Boot 4.x |
| auth, auth-service | Java 25 | Spring Boot 4.x |
| gateway-service | Java 25 | Spring Boot 4.x + Spring Cloud Gateway |
| product, product-service | Java 25 | Spring Boot 4.x |
| order, order-service | Java 21 | Spring Boot 3.x |
| exchange, exchange-service | Python 3 | FastAPI |

### Infraestrutura

| Componente | Tecnologia |
|---|---|
| Containers | Docker + Docker Compose |
| Orquestração | Kubernetes (AWS EKS) |
| Registry | Docker Hub |
| Banco de dados | PostgreSQL (Flyway migrations) |
| CI/CD | Jenkins |
| Segurança | OWASP Dependency Check |
| Observabilidade | Prometheus + Grafana |
| Load Balancing | Nginx (compose) + ALB (EKS) |

## Arquitetura

```
Internet
   │
   ▼
 Nginx (porta 80)
   │
   ▼
Gateway Service ──── JWT validation
   │
   ├──► Account Service ──► PostgreSQL
   ├──► Auth Service    ──► Account Service
   ├──► Product Service ──► PostgreSQL
   └──► Order Service   ──► Product Service
                        └──► Exchange Service ──► API Externa
```

## Organização do Repositório

```
api/
├── account/          # biblioteca de contrato
├── account-service/  # microsserviço
├── auth/
├── auth-service/
├── exchange/
├── exchange-service/
├── gateway-service/
├── order/
├── order-service/
├── product/
├── product-service/
├── postgres-service/ # manifests k8s do banco
├── setup/            # prometheus, grafana, nginx configs
├── compose.yaml
└── Jenkinsfile       # pipeline raiz
```
