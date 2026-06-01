# Product API

## Objetivo

API RESTful para gerenciamento de produtos da plataforma, exposta via gateway e protegida por autenticação JWT.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Linguagem | Java 25 |
| Framework | Spring Boot 4.x + Spring Data JPA |
| Banco de dados | PostgreSQL |
| Migrations | Flyway |
| Observabilidade | Micrometer + Prometheus |
| Utilitários | Lombok |

## Endpoints

Todas as rotas são prefixadas com `/products`. O serviço escuta na porta `8080` e é acessado via gateway em `http://localhost/products`.

| Método | Rota | Descrição | Resposta |
|---|---|---|---|
| `POST` | `/products` | Cria um novo produto | `201 Created` + header `Location` |
| `GET` | `/products` | Lista todos os produtos | `200 OK` + `ProductOut[]` |
| `GET` | `/products?name=x` | Busca por nome (case-insensitive) | `200 OK` + `ProductOut[]` |
| `GET` | `/products/{id}` | Busca produto por ID | `200 OK` + `ProductOut` |
| `DELETE` | `/products/{id}` | Remove um produto | `204 No Content` |

## Payload

=== "Request (POST)"
    ```json
    {
        "name": "Tomato",
        "price": 10.12,
        "unit": "kg"
    }
    ```

=== "Response (GET)"
    ```json
    [
        {
            "id": "0195abfb-7074-73a9-9d26-b4b9fbaab0a8",
            "name": "Tomato",
            "price": 10.12,
            "unit": "kg"
        }
    ]
    ```

## Validações

| Campo | Tipo | Regra |
|---|---|---|
| `name` | `String` | `@NotBlank` |
| `price` | `Double` | `@NotNull` + `@Positive` |
| `unit` | `String` | `@NotBlank` |

## Schema do Banco

```sql
CREATE SCHEMA IF NOT EXISTS products;

CREATE TABLE products.products (
    id    VARCHAR(36)    PRIMARY KEY,
    name  VARCHAR(256)   NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    unit  VARCHAR(64)    NOT NULL
);
```

## Observabilidade

Métricas no formato Prometheus expostas em:

```
GET /product/actuator/prometheus
```

## Código Fonte

[pma2026-carol-fernando-thais/product-service](https://github.com/pma2026-carol-fernando-thais/product-service)
