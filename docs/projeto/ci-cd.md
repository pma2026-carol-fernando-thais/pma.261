# CI/CD

## Objetivo

Automatizar o processo de build, testes, publicação e deploy dos serviços.

## Ferramenta

- Jenkins

## Pipeline

Cada microsserviço executa as seguintes etapas:

### 1. Source Code Management

Checkout do código fonte.

### 2. Build

Compilação do projeto Maven.

### 3. Vulnerability Scan

Análise de dependências utilizando OWASP Dependency Check.

### 4. Build Docker Image

Construção da imagem Docker.

### 5. Push Docker Hub

Publicação da imagem no Docker Hub.

### 6. Deploy Kubernetes

Atualização automática do ambiente EKS.

## Benefícios

- Redução de erros manuais
- Padronização dos deploys
- Maior segurança
- Entrega contínua