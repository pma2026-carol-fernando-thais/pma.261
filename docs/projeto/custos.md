# Custos

Estimativa de custo mensal para ambiente de demonstração na AWS. Região: `us-east-1`. Referência: 2026-05-29.

## Premissas

| Item | Premissa |
|---|---|
| Cluster Kubernetes | 1 cluster Amazon EKS |
| Worker nodes | 2 instâncias EC2 `t3.medium` |
| Banco de dados | Amazon RDS for PostgreSQL Single-AZ `db.t4g.micro` |
| Registry | Amazon ECR (5 GB) |
| Load Balancer | 1 Application Load Balancer |
| Observabilidade | CloudWatch Logs + métricas básicas |
| Tráfego | Baixo volume — ambiente acadêmico |

## Plano de Custos

| Serviço AWS | Uso previsto | Estimativa mensal |
|---|---|---:|
| Amazon EKS control plane | 1 cluster | USD 73.00 |
| EC2 worker nodes | 2 × `t3.medium` | USD 60.74 |
| EBS dos worker nodes | 2 × 20 GB | USD 3.20 |
| Amazon RDS PostgreSQL | 1 `db.t4g.micro` Single-AZ | USD 11.68 |
| RDS storage | 20 GB gp3 | USD 2.30 |
| Application Load Balancer | 1 ALB | USD 22.27 |
| Amazon ECR | 5 GB imagens | USD 0.50 |
| CloudWatch Logs | 5 GB ingeridos | USD 2.50 |
| CloudWatch Alarms | 3 alarmes | USD 0.30 |
| Data transfer out | 10 GB | USD 0.90 |
| **Total estimado** | | **USD 177.39/mês** |

!!! warning "Estimativa"
    Não inclui impostos, Free Tier, NAT Gateway, Route 53, snapshots extras ou aumento de réplicas durante testes de carga.

## Otimização de Custos

| Ação | Impacto |
|---|---|
| Desligar ambientes fora do período de teste | Reduz EC2, EKS, RDS e Load Balancer |
| Usar réplicas mínimas em desenvolvimento | Reduz custo dos worker nodes |
| Configurar lifecycle policy no ECR | Remove imagens antigas |
| Limitar retenção de logs no CloudWatch | Evita crescimento contínuo |
| Usar RDS Single-AZ no ambiente acadêmico | Mais barato que Multi-AZ |

## Referências

- [AWS Pricing Calculator](https://calculator.aws/)
- [Amazon EKS pricing](https://aws.amazon.com/eks/pricing/)
- [Amazon RDS for PostgreSQL pricing](https://aws.amazon.com/rds/postgresql/pricing/)
- [Amazon EC2 On-Demand pricing](https://aws.amazon.com/ec2/pricing/on-demand/)
