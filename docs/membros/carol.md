# Carol

## Responsabilidades

- Product API e Product Service
- Configuração de CI/CD (Jenkinsfiles de account-service, auth-service, gateway-service, product-service)
- Kubernetes manifests (account-service, auth-service, gateway-service, product-service, postgres-service)
- Infraestrutura: submodules, .gitmodules, namespace k8s

## Repositórios

- [product](https://github.com/pma2026-carol-fernando-thais/product)
- [product-service](https://github.com/pma2026-carol-fernando-thais/product-service)

## Bottlenecks Implementados

### Observabilidade (Prometheus + Grafana)

Integração de Micrometer + Prometheus no `product-service` e `gateway-service`. Métricas expostas em `/product/actuator/prometheus` e coletadas pelo Prometheus configurado no `compose.yaml`.

### Vulnerability Scan

Stage OWASP Dependency Check adicionado em todos os Jenkinsfiles (`account-service`, `auth-service`, `gateway-service`, `product-service`). Falha o build se CVSS ≥ 11.

### Credenciais Seguras (envsubst)

Substituição de credenciais via `envsubst` nos manifests Kubernetes — senhas nunca ficam hardcoded no repositório.

### Kubernetes Manifests Completos

Criação dos manifests `k8s/k8s.yaml` para `account-service`, `auth-service`, `gateway-service` e `product-service`, todos no namespace `store` com Secret, ConfigMap, Deployment e Service.

### Rota do Product no Gateway

Adição da rota `/products/**` no `gateway-service`, tornando o product-service acessível pelo fluxo completo: `nginx → gateway → product-service`.
