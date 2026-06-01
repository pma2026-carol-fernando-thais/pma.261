# Thaís Rocha Silveira

## Responsabilidades

- Order API e Order Service
- Documentação relacionada ao módulo de pedidos

## Repositórios

- [order](https://github.com/pma2026-carol-fernando-thais/order.git)
- [order-service](https://github.com/pma2026-carol-fernando-thais/order-service.git)

## Bottlenecks Implementados

### Kubernetes Health Checks

Configuração dos probes `liveness` e `readiness` no manifest k8s do `order-service`, garantindo que o Kubernetes reinicie pods com falha e só encaminhe tráfego para pods prontos.

### Vulnerability Scan

Stage OWASP Dependency Check adicionado no Jenkinsfile do `order-service`. Falha o build se alguma dependência tiver CVSS ≥ 11.

### Dependências de Build

O `order-service` depende dos módulos `order` e `product`. O pipeline garante que ambos sejam compilados antes do build do serviço:

```groovy
build job: 'order',   wait: true
build job: 'product', wait: true
```

### Credenciais Seguras

Substituição de credenciais hardcoded no manifest Kubernetes por variáveis injetadas via Jenkins Credentials em runtime.
