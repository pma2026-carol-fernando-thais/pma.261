# Architecture

## Visão Geral

A plataforma segue uma arquitetura baseada em microsserviços.

```text
Cliente
   |
Gateway Service
   |
------------------------------------------------
|          |           |           |            |
Auth    Account    Product     Order      Exchange
```

## Componentes

### Gateway Service

Responsável pelo roteamento das requisições.

### Auth Service

Responsável pela autenticação dos usuários.

### Account Service

Responsável pelo gerenciamento de contas.

### Product Service

Responsável pelo gerenciamento de produtos.

### Order Service

Responsável pelo gerenciamento de pedidos.

### Exchange Service

Responsável pela consulta de informações de câmbio.

## Banco de Dados

A persistência é realizada utilizando PostgreSQL.

## Containerização

Todos os serviços são executados em containers Docker.

## Orquestração

O gerenciamento dos containers é realizado através do Kubernetes executando no Amazon EKS.