# Exchange API

## Objetivo

Disponibilizar taxas de câmbio em tempo real para os demais serviços da plataforma, consultando um provedor externo.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Linguagem | Python 3 |
| Framework | FastAPI |
| Provedor externo | [AwesomeAPI](https://economia.awesomeapi.com.br) |
| Containerização | Docker |

## Endpoints

| Método | Rota | Descrição | Auth |
|---|---|---|---|
| `GET` | `/health` | Health check | Não |
| `GET` | `/exchanges/{from}/{to}` | Taxa de câmbio entre duas moedas | Header `id-account` |
| `GET` | `/exchange/{currency}` | Taxa USD → moeda (legado) | Não |

### GET /exchanges/{from}/{to}

Retorna as taxas de compra e venda entre dois pares de moedas. Requer usuário autenticado via header.

**Headers:**

| Header | Obrigatório |
|---|---|
| `id-account` ou `X-ACCOUNT-ID` | Sim |

**Resposta:**

```json
{
    "sell": 5.823400,
    "buy": 5.819100,
    "date": "2026-05-31 10:00:00",
    "id-account": "uuid-do-usuario"
}
```

### GET /exchange/{currency}

Endpoint legado. Retorna a taxa de venda do USD para a moeda informada.

```json
{
    "currency": "BRL",
    "rate": 5.8234
}
```

## Validações

- Código de moeda deve ter exatamente 3 letras (`min_length=3`, `max_length=3`)
- Par não suportado retorna `422 Unprocessable Entity`
- Provedor indisponível retorna `502 Bad Gateway`
- Usuário não autenticado retorna `401 Unauthorized`

## Código Fonte

[pma2026-carol-fernando-thais/exchange-service](https://github.com/pma2026-carol-fernando-thais/exchange-service.git)
