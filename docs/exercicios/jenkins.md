# Jenkins

## Objetivo

Automatizar o processo de integração contínua e entrega contínua (CI/CD).

## Pipeline Implementada

Cada serviço possui pipeline responsável por:

1. Checkout do código
2. Build Maven
3. Vulnerability Scan
4. Build da imagem Docker
5. Push para Docker Hub
6. Deploy no Kubernetes

## Ferramentas Utilizadas

- Jenkins
- Docker
- Docker Hub
- AWS EKS

## Evidências

Adicionar screenshots dos pipelines executando.