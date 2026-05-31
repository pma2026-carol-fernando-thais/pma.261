# Bottlenecks

## 1. Kubernetes Health Checks

### Problema

Os endpoints:

- /actuator/health/liveness
- /actuator/health/readiness

estavam configurados no Kubernetes, porém o Spring Boot Actuator não estava presente no projeto.

### Impacto

Os pods poderiam entrar em loop de reinicialização.

### Solução

Foi adicionada a dependência:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

---

## 2. Vulnerability Scan

### Problema

O pipeline não verificava vulnerabilidades das dependências Maven.

### Impacto

Bibliotecas vulneráveis poderiam ser enviadas para produção.

### Solução

Foi adicionada a etapa:

```groovy
stage('Vulnerability Scan') {
    steps {
        sh 'mvn -B org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=11'
    }
}
```

---

## 3. Dependências de Build

### Problema

O serviço order-service dependia dos módulos:

- order
- product

Porém apenas o módulo order era compilado.

### Impacto

Falha de build em ambientes limpos.

### Solução

```groovy
build job: 'order', wait: true
build job: 'product', wait: true
```

---

## 4. Credenciais Hardcoded

### Problema

As credenciais do banco estavam fixas no Kubernetes.

```yaml
DB_USER: postgres
DB_PASSWORD: postgres
```

### Impacto

Baixa segurança e manutenção difícil.

### Solução

Utilização de Jenkins Credentials e envsubst.

```yaml
DB_USER: ${DB_USER}
DB_PASSWORD: ${DB_PASSWORD}
```