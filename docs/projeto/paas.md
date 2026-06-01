# PaaS

PaaS (Platform as a Service) é o uso de plataformas gerenciadas que eliminam a necessidade de operar infraestrutura de baixo nível.

## Serviços Utilizados

| Serviço | Como o projeto usa | Responsabilidade transferida para o provedor |
|---|---|---|
| **Amazon EKS** | Orquestração dos containers e deployments Kubernetes | Control plane, API server gerenciado |
| **Amazon RDS for PostgreSQL** | Banco relacional da aplicação | Backup, patching, disponibilidade e recuperação |
| **Amazon ECR** | Registry de imagens Docker | Armazenamento, disponibilidade e distribuição |
| **Elastic Load Balancing** | Entrada HTTP para o gateway | Alta disponibilidade do balanceador |
| **Amazon CloudWatch** | Logs, métricas e alarmes | Coleta, armazenamento e consulta de telemetria |
| **Docker Hub** | Alternativa de registry público | Hospedagem e distribuição de imagens |

## Benefício

O uso desses serviços reduz o trabalho operacional do grupo: em vez de manter manualmente control plane Kubernetes, registry, banco de dados e stack de logs, o time foca no desenvolvimento dos microsserviços, pipeline e configuração declarativa.
