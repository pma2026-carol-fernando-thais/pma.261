# Overview

## Objetivo

O projeto consiste no desenvolvimento de uma plataforma baseada em microsserviços utilizando práticas modernas de desenvolvimento, integração contínua e implantação em nuvem.

A solução foi desenvolvida durante a disciplina PMA.261 com o objetivo de aplicar conceitos de:

- Microsserviços
- Containers
- Kubernetes
- Integração Contínua (CI)
- Entrega Contínua (CD)
- Cloud Computing
- Observabilidade
- Segurança

## Tecnologias Utilizadas

### Backend

- Java 21
- Spring Boot
- Spring Data JPA
- Spring Cloud OpenFeign

### Infraestrutura

- Docker
- Docker Hub
- Kubernetes
- AWS EKS

### Banco de Dados

- PostgreSQL

### DevOps

- Jenkins
- OWASP Dependency Check

## Arquitetura

A aplicação foi dividida em serviços independentes responsáveis por domínios específicos do negócio.

- Account Service
- Auth Service
- Gateway Service
- Product Service
- Order Service
- Exchange Service

Cada serviço possui seu próprio ciclo de build, deploy e monitoramento.