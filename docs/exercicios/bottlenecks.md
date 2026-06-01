# Bottlenecks

## 1. Observabilidade

### Problema

Sem monitoramento, é impossível identificar gargalos de performance, erros silenciosos e uso de recursos em produção.

### Impacto

Incidentes detectados apenas pelo usuário final; ausência de dados para tomada de decisão.

### Solução

Integração de **Prometheus + Grafana** via Micrometer:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
    <scope>runtime</scope>
</dependency>
```

Métricas expostas em `/product/actuator/prometheus` e `/gateway/actuator/prometheus`, coletadas pelo Prometheus e visualizadas no Grafana.

---

## 2. Vulnerability Scan

### Problema

O pipeline não verificava vulnerabilidades das dependências Maven.

### Impacto

Bibliotecas vulneráveis poderiam ser enviadas para produção.

### Solução

Stage adicionado em todos os Jenkinsfiles:

```groovy
stage('Vulnerability Scan') {
    steps {
        sh 'mvn -B org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=11'
    }
}
```

---

## 3. Kubernetes Health Checks

### Problema

Os endpoints `/actuator/health/liveness` e `/actuator/health/readiness` estavam configurados no Kubernetes, porém o Spring Boot Actuator não estava presente.

### Impacto

Os pods poderiam entrar em loop de reinicialização.

### Solução

Adição da dependência `spring-boot-starter-actuator` e configuração dos probes no Deployment.

---

## 4. Credenciais Expostas

### Problema

Credenciais do banco hardcoded nos manifests Kubernetes:

```yaml
DB_USER: postgres
DB_PASSWORD: postgres
```

### Impacto

Baixa segurança — senha visível no repositório e difícil de rotacionar.

### Solução

Utilização de Jenkins Credentials + `envsubst` para injeção em runtime:

```yaml
stringData:
  DATABASE_USERNAME: ${DB_USER}
  DATABASE_PASSWORD: ${DB_PASSWORD}
```

---

## 5. Caching

### Problema

O `exchange-service` consulta uma API externa (`awesomeapi.com.br`) a cada requisição de câmbio, introduzindo latência (~300ms) e risco de indisponibilidade por rate limit.

### Impacto

Cada pedido criado pelo `order-service` dispara uma chamada externa desnecessária para a mesma taxa de câmbio.

### Solução

Implementar **Redis** no `exchange-service` com TTL de 60 segundos por par de moedas:

```python
cache_key = f"rate:{source}:{target}"
cached = redis_client.get(cache_key)
if cached:
    return ProviderRate(**json.loads(cached))
# ... busca externa ...
redis_client.setex(cache_key, 60, json.dumps({...}))
```

---

## 6. Load Balancing e Escalabilidade

### Problema

Picos de tráfego sobrecarregam instâncias fixas do gateway.

### Impacto

Latência alta e possível indisponibilidade em momentos de pico.

### Solução

- **Nginx** como load balancer no ambiente Docker Compose
- **Service tipo LoadBalancer** no Kubernetes (EKS provisiona ALB)
- **HPA** no gateway: escala de 1 a 10 réplicas quando CPU > 50%

```bash
kubectl apply -f api/gateway-service/k8s/hpa.yaml
```
