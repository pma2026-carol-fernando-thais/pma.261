# Load Testing

Este projeto usa o `gateway-service` como alvo do teste de carga. O objetivo e gerar trafego para o endpoint `GET /health-check` e observar o `HorizontalPodAutoscaler` escalar o deployment `gateway` com base no uso de CPU.

Os manifests ficam em:

```text
api/gateway-service/k8s/
```

| Arquivo | Funcao |
|---|---|
| `k8s.yaml` | Cria o `Deployment` e o `Service` do gateway |
| `hpa.yaml` | Cria o HPA do gateway com alvo de 50% de CPU |
| `load-generator.yaml` | Cria um pod `busybox` que gera requisicoes continuamente |

## Pre-requisitos

- Cluster Kubernetes acessivel via `kubectl`.
- `metrics-server` ativo, pois o HPA usa metricas de CPU.
- Imagem do gateway publicada e acessivel pelo cluster.

!!! note "Imagem do gateway"
    O manifesto usa `humbertosandmann/gateway:latest`. Se a imagem publicada no seu ambiente tiver outro nome, altere o campo `image` em `api/gateway-service/k8s/k8s.yaml`.

## Deploy do gateway

```bash
kubectl apply -f api/gateway-service/k8s/k8s.yaml
```

Verifique os pods:

```bash
kubectl get pods -l app=gateway
```

## Criar o HPA

```bash
kubectl apply -f api/gateway-service/k8s/hpa.yaml
```

Comando equivalente:

```bash
kubectl autoscale deployment gateway --cpu-percent=50 --min=1 --max=10
```

Verifique o HPA:

```bash
kubectl get hpa
```

## Monitorar durante o video

Terminal 1:

```bash
watch -n 1 'kubectl get hpa'
```

Terminal 2:

```bash
watch -n 1 'kubectl get pods -l app=gateway'
```

No PowerShell, use:

```powershell
while ($true) { kubectl get hpa; Start-Sleep -Seconds 1; Clear-Host }
while ($true) { kubectl get pods -l app=gateway; Start-Sleep -Seconds 1; Clear-Host }
```

## Gerar carga

Terminal 3:

```bash
kubectl apply -f api/gateway-service/k8s/load-generator.yaml
```

O pod executa um loop chamando:

```text
http://gateway/health-check
```

Tambem e possivel executar o comando interativo:

```bash
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://gateway/health-check > /dev/null; done"
```

## Limpeza

```bash
kubectl delete pod load-generator --ignore-not-found
kubectl delete -f api/gateway-service/k8s/hpa.yaml --ignore-not-found
kubectl delete -f api/gateway-service/k8s/k8s.yaml --ignore-not-found
```
