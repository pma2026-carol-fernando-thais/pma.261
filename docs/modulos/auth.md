# auth (biblioteca)

Biblioteca de Feign client para o [`auth-service`](auth-service.md). Outros serviços importam este artefato para se comunicar com o serviço de autenticação via HTTP sem escrever código boilerplate REST.

---

## Visão Geral

O módulo `auth` expõe uma interface `@FeignClient` (`AuthController`) que mapeia cada endpoint de autenticação para um método Java tipado. Qualquer serviço Spring Boot que precise autenticar usuários, resolver tokens JWT ou consultar identidade de conta pode depender desta biblioteca.

---

## Stack

| Camada | Tecnologia |
|---|---|
| Linguagem | Java 25 |
| Framework | Spring Boot 4.x + Spring Cloud OpenFeign |
| Utilitários | Lombok |

---

## Endpoints mapeados

O cliente aponta para `http://auth:8080` (container `auth-service` na rede interna Docker).

| Método | Caminho | Descrição |
|---|---|---|
| `POST` | `/auth/login` | Autenticar com e-mail + senha. Retorna header `Set-Cookie` com JWT. |
| `POST` | `/auth/register` | Criar nova conta. Retorna `201 Created`. |
| `GET` | `/auth/whoiam` | Retornar detalhes da conta para um ID fornecido. |
| `POST` | `/auth/solve` | Validar token JWT e retornar o `idAccount` associado. |
| `GET` | `/auth/logout` | Limpar o cookie de autenticação (`maxAge=0`). |
| `GET` | `/auth/health-check` | Liveness probe. Retorna `200 OK`. |

---

## Modelos de Dados

### `LoginIn`

```json
{
  "email": "user@example.com",
  "password": "secret"
}
```

### `RegisterIn`

```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "secret"
}
```

### `TokenOut`

Usado como body na requisição para `/auth/solve`:

```json
{
  "token": "<jwt>"
}
```

A resposta é um mapa:

```json
{
  "idAccount": "<uuid>"
}
```

---

## Cookie JWT

Após login bem-sucedido, o auth-service define um cookie `HttpOnly`, `Secure`, `SameSite=None` chamado `__store_jwt_token` válido por **24 horas**. O cookie é limpo automaticamente no logout com `maxAge=0`.

A constante `AuthController.AUTH_COOKIE_TOKEN` armazena o nome do cookie e pode ser usada em outros serviços para ler ou limpar o cookie sem hardcoding da string.

---

## Como Usar

**1. Adicionar a dependência no `pom.xml`:**

```xml
<dependency>
    <groupId>store</groupId>
    <artifactId>auth</artifactId>
    <version>1.0.0</version>
</dependency>
```

**2. Habilitar Feign clients na aplicação Spring Boot:**

```java
@EnableFeignClients(basePackages = "store.auth")
@SpringBootApplication
public class YourApplication { ... }
```

**3. Injetar e usar:**

```java
@Autowired
private AuthController authController;

// Resolver um token para um ID de conta
ResponseEntity<Map<String, String>> result = authController.solveToken(
    TokenOut.builder().token(jwt).build()
);
String idAccount = result.getBody().get("idAccount");
```

---

## Build

```bash
cd api/auth
mvn clean install
```

O artefato é instalado no repositório Maven local e pode ser consumido pelos outros serviços neste monorepo.
