# Order API

## Objetivo

API RESTful para gerenciamento de pedidos da plataforma, integrando product-service e exchange-service para calcular totais em múltiplas moedas.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Linguagem | Java 21 |
| Framework | Spring Boot 3.x + Spring Data JPA |
| Comunicação | Spring Cloud OpenFeign |
| Banco de dados | PostgreSQL |

## Integrações

| Serviço | Como usa |
|---|---|
| `product-service` | Consulta dados e preço do produto via Feign (`http://product-service:8080`) |
| `exchange-service` | Obtém taxa de câmbio para conversão de moeda via Feign (`http://exchange:8080`) |

## Funcionalidades

- Criação de pedidos com múltiplos produtos
- Consulta de pedidos por ID e por conta
- Cálculo do total em moeda local via taxa de câmbio
- Integração com product-service e exchange-service

## Código Fonte

[pma2026-carol-fernando-thais/order-service](https://github.com/pma2026-carol-fernando-thais/order-service.git)
