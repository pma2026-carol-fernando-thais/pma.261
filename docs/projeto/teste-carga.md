# Teste de Carga

## Objetivo

Validar o comportamento da plataforma sob carga e demonstrar a escalabilidade automática do gateway via HPA.

## Abordagem

O teste usa um pod `busybox` no próprio cluster Kubernetes para gerar requisições contínuas ao gateway, observando o HPA escalar os pods automaticamente.

## Pré-requisitos

- Cluster Kubernetes acessível via `kubectl`
- `metrics-server` ativo (necessário para o HPA)
- Imagem do gateway publicada no registry

## Deploy do Gateway com HPA

```bash
# Aplicar deployment e service do gateway
kubectl apply -f api/gateway-service/k8s/k8s.yaml

# Criar HPA (escala de 1 a 10 pods quando CPU > 50%)
kubectl apply -f api/gateway-service/k8s/hpa.yaml
```

## Gerar Carga

```bash
# Iniciar gerador de carga
kubectl apply -f api/gateway-service/k8s/load-generator.yaml
```

Ou interativamente:

```bash
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- \
  /bin/sh -c "while sleep 0.01; do wget -q -O- http://gateway/health-check > /dev/null; done"
```

## Monitorar Escalabilidade

=== "Linux/macOS"
    ```bash
    # Terminal 1 — HPA
    watch -n 1 'kubectl get hpa'

    # Terminal 2 — Pods
    watch -n 1 'kubectl get pods -l app=gateway'
    ```

=== "PowerShell"
    ```powershell
    # HPA
    while ($true) { kubectl get hpa; Start-Sleep -Seconds 1; Clear-Host }

    # Pods
    while ($true) { kubectl get pods -l app=gateway; Start-Sleep -Seconds 1; Clear-Host }
    ```

## Comportamento Esperado

À medida que a carga aumenta:

1. CPU dos pods do gateway ultrapassa 50%
2. HPA detecta o aumento e escala o Deployment
3. Novos pods são criados (até máximo de 10)
4. Ao remover a carga, os pods são removidos gradualmente

## Limpeza

```bash
kubectl delete pod load-generator --ignore-not-found
kubectl delete -f api/gateway-service/k8s/hpa.yaml --ignore-not-found
```
