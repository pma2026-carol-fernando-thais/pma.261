# Fluxo de Autenticação

A autenticação é baseada em **cookies HttpOnly** com tokens JWT assinados por HMAC-SHA256. O `auth-service` é o único emissor e validador de tokens.

---

## Visão Geral

```mermaid
sequenceDiagram
    participant C as Client (Browser)
    participant GW as gateway-service
    participant AU as auth-service
    participant AC as account-service

    Note over C,AC: Login
    C->>GW: POST /auth/login {email, password}
    GW->>AU: POST /auth/login (rota aberta)
    AU->>AC: POST /accounts/login {email, password}
    AC-->>AU: AccountOut {id, name, email}
    AU->>AU: gerar JWT (HMAC-SHA256, 24h)
    AU-->>GW: 200 OK + Set-Cookie: __store_jwt_token
    GW-->>C: 200 OK + Set-Cookie: __store_jwt_token

    Note over C,AC: Requisição protegida
    C->>GW: GET /accounts (cookie enviado automaticamente)
    GW->>AU: POST /auth/solve {token}
    AU-->>GW: {idAccount: "uuid"}
    GW->>AC: GET /accounts + header id-account
    AC-->>GW: List<AccountOut>
    GW-->>C: 200 OK + JSON

    Note over C,AC: Logout
    C->>GW: GET /auth/logout
    GW->>AU: GET /auth/logout
    AU-->>GW: Set-Cookie: __store_jwt_token; maxAge=0
    GW-->>C: Cookie removido
```

---

## Etapas Detalhadas

### 1. Login

| Passo | Ação |
|---|---|
| Cliente | `POST /auth/login` com `{ email, password }` |
| Gateway | Encaminha para `auth-service` sem validação (rota aberta) |
| auth-service | Chama `account-service` via Feign para verificar credenciais |
| account-service | Compara `SHA-256(password)` com hash armazenado |
| auth-service | Se válido, gera JWT assinado (HS256) com validade de 24h |
| Gateway | Repassa o `Set-Cookie` para o cliente |

### 2. Requisição Protegida

| Passo | Ação |
|---|---|
| Cliente | Envia qualquer requisição — o browser inclui o cookie automaticamente |
| Gateway | `AuthorizationFilter` extrai o token do cookie `__store_jwt_token` |
| Gateway | Chama `POST /auth/solve` com o token |
| auth-service | Valida assinatura e expiração do JWT |
| auth-service | Retorna `{ idAccount: "uuid" }` |
| Gateway | Injeta headers `id-account` e `Authorization: Bearer <jwt>` |
| Gateway | Encaminha a requisição para o serviço upstream |

### 3. Logout

| Passo | Ação |
|---|---|
| Cliente | `GET /auth/logout` |
| auth-service | Retorna `Set-Cookie: __store_jwt_token; maxAge=0` |
| Browser | Remove o cookie automaticamente |

---

## Token JWT

### Payload

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "sub": "Jane Doe",
  "email": "jane@example.com",
  "iss": "Insper::PMA",
  "nbf": 1716192000,
  "exp": 1716278400
}
```

### Propriedades do Cookie

| Atributo | Valor |
|---|---|
| Nome | `__store_jwt_token` |
| `HttpOnly` | configurável |
| `Secure` | `true` |
| `SameSite` | `None` |
| `Path` | `/` |
| Validade | 24 horas |

---

## Rotas Abertas vs. Protegidas

| Rota | Autenticação |
|---|---|
| `POST /auth/login` | Aberta |
| `POST /auth/register` | Aberta |
| `GET /auth/logout` | Aberta |
| Qualquer outra | Requer cookie JWT válido |

---

## Segurança

!!! warning "Chave JWT"
    A variável `JWT_SECRET_KEY` deve ser uma chave HMAC aleatória e forte, codificada em Base64. Nunca use o valor padrão em produção.

!!! info "HttpOnly"
    Com `JWT_HTTP_ONLY=true`, o cookie não é acessível via JavaScript, prevenindo ataques XSS. Em desenvolvimento pode ser `false` para facilitar inspeção.
