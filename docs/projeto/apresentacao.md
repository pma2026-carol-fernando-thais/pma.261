# Apresentação

## Integrantes

- Thaís Rocha Silveira
- Carol
- Fernando

## Tema

Plataforma de e-commerce baseada em microsserviços com CI/CD automatizado, orquestração Kubernetes e deploy na AWS.

## Conteúdo

### 1. Introdução

Objetivos do projeto, tecnologias utilizadas e organização do grupo.

### 2. Arquitetura

Diagrama dos microsserviços, comunicação entre eles e fluxo de uma requisição completa (login → criar produto → criar pedido com câmbio).

### 3. CI/CD

Demonstração do pipeline Jenkins: SCM → Dependencies → Build → Vulnerability Scan → Push Docker Hub → Deploy EKS.

### 4. Kubernetes

Manifests k8s em namespace `store`, HPA do gateway e demonstração dos pods rodando no cluster.

### 5. Bottlenecks

Gargalos identificados e soluções implementadas:

- Observabilidade com Prometheus + Grafana
- Vulnerability Scan com OWASP
- Escalabilidade automática com HPA
- Credenciais seguras via envsubst
- Caching com Redis no exchange-service

### 6. Teste de Carga

Demonstração do HPA escalando o gateway sob carga gerada pelo `load-generator.yaml`.

### 7. Custos

Estimativa de USD 177,39/mês para o ambiente de demonstração na AWS.

### 8. Conclusão

Resultados obtidos, aprendizados e desafios encontrados.
