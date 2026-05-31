# Costs & PaaS

Esta pagina documenta o plano de custos e o uso de PaaS do projeto Store. Os valores abaixo sao estimativas para um ambiente pequeno de demonstracao em AWS e devem ser revisados na AWS Pricing Calculator antes da entrega final.

Data da estimativa: 2026-05-29.

## Premissas

| Item | Premissa |
|---|---|
| Regiao | `us-east-1` |
| Mes de referencia | 730 horas |
| Cluster Kubernetes | 1 cluster Amazon EKS em suporte padrao |
| Worker nodes | 2 instancias EC2 pequenas para os pods da aplicacao |
| Banco de dados | 1 Amazon RDS for PostgreSQL Single-AZ |
| Registry | Amazon ECR para imagens Docker |
| Entrada HTTP | 1 Application Load Balancer ou LoadBalancer criado pelo Kubernetes |
| Observabilidade AWS | CloudWatch Logs e metricas basicas |
| Trafego | Baixo volume, adequado para demonstracao academica |

## Plano de Custos

| Servico AWS | Uso previsto | Calculo base | Estimativa mensal |
|---|---:|---:|---:|
| Amazon EKS control plane | 1 cluster | USD 0.10/h * 730h | USD 73.00 |
| EC2 worker nodes | 2 * `t3.medium` | 2 * USD 0.0416/h * 730h | USD 60.74 |
| EBS dos worker nodes | 2 volumes de 20 GB | 40 GB * USD 0.08/GB-mes | USD 3.20 |
| Amazon RDS PostgreSQL | 1 `db.t4g.micro` Single-AZ | USD 0.016/h * 730h | USD 11.68 |
| RDS storage | 20 GB gp3 | 20 GB * USD 0.115/GB-mes | USD 2.30 |
| Application Load Balancer | 1 ALB + 1 LCU leve | USD 0.0225/h + USD 0.008/LCU-h | USD 22.27 |
| Amazon ECR | 5 GB de imagens privadas | 5 GB * USD 0.10/GB-mes | USD 0.50 |
| CloudWatch Logs | 5 GB ingeridos | 5 GB * USD 0.50/GB | USD 2.50 |
| CloudWatch Alarms | 3 alarmes basicos | 3 * USD 0.10/mes | USD 0.30 |
| Data transfer out | 10 GB para internet | 10 GB * USD 0.09/GB | USD 0.90 |
| **Total estimado** |  |  | **USD 177.39/mes** |

!!! warning "Estimativa"
    Este valor nao inclui impostos, creditos academicos, Free Tier, variacao cambial, NAT Gateway, Route 53, ACM, snapshots extras, trafego entre AZs, backups acima do limite incluso ou aumento de replicas durante testes de carga.

## Servicos Incluidos

### Amazon EKS

O projeto usa Kubernetes para executar os microservicos (`gateway-service`, `account-service`, `auth-service`, `product-service`, `order-service`, `exchange-service`) e recursos auxiliares. Em EKS, a AWS gerencia o control plane do Kubernetes; o projeto paga separadamente pelo cluster e pelos recursos usados pelos worker nodes.

### Amazon EC2 e EBS

Os worker nodes executam os pods da aplicacao. Para demonstracao, o plano assume 2 instancias pequenas com volumes EBS de 20 GB. Em producao, o dimensionamento deve considerar:

- requests e limits dos pods;
- quantidade minima de replicas;
- uso do HPA;
- margem para rollout sem indisponibilidade.

### Amazon RDS for PostgreSQL

O repositorio possui um `postgres-service` para Kubernetes, mas o proprio modulo indica que, em ambiente cloud, a opcao recomendada e usar banco gerenciado. Portanto, o plano de custos considera RDS for PostgreSQL para reduzir operacao manual de backup, patching, disponibilidade e recuperacao.

### Amazon ECR

O ECR armazena as imagens Docker publicadas pelo pipeline Jenkins. O custo principal e armazenamento das imagens. Pulls na mesma regiao para recursos AWS nao geram custo adicional de transferencia no caso comum.

### Elastic Load Balancing

Quando o gateway e exposto no Kubernetes por um `Service` do tipo `LoadBalancer` ou por Ingress, a AWS pode provisionar um Load Balancer. O plano considera um Application Load Balancer para receber trafego HTTP e encaminhar para o gateway.

### Amazon CloudWatch

O CloudWatch centraliza logs, metricas e alarmes operacionais. Para este projeto, o uso previsto e:

- logs dos pods e componentes principais;
- metricas de CPU/memoria usadas para observabilidade;
- alarmes basicos de indisponibilidade e consumo.

## Otimizacao de Custos

| Acao | Impacto |
|---|---|
| Desligar ambientes fora do periodo de teste | Reduz EC2, EKS, RDS e Load Balancer |
| Usar replicas minimas em desenvolvimento | Reduz custo dos worker nodes |
| Configurar lifecycle policy no ECR | Remove imagens antigas e reduz armazenamento |
| Limitar retencao de logs no CloudWatch | Evita crescimento continuo de custo |
| Usar RDS Single-AZ no ambiente academico | Mais barato que Multi-AZ |
| Usar budgets e alertas | Evita surpresa de cobranca |

## PaaS

PaaS e o uso de uma plataforma gerenciada para executar ou apoiar a aplicacao sem que o time precise operar diretamente toda a infraestrutura de baixo nivel.

No projeto, os principais usos de PaaS/IaaS gerenciado sao:

| Servico | Como o projeto usa | Responsabilidade transferida para a AWS |
|---|---|---|
| Amazon EKS | Orquestracao dos containers e deployments Kubernetes | Control plane, API server gerenciado e integracao com servicos AWS |
| Amazon RDS for PostgreSQL | Banco relacional da aplicacao | Backup, patching, disponibilidade, storage gerenciado e recuperacao |
| Amazon ECR | Registro de imagens Docker | Armazenamento, disponibilidade e distribuicao das imagens |
| Elastic Load Balancing | Entrada HTTP para o gateway | Alta disponibilidade do balanceador e roteamento para targets saudaveis |
| CloudWatch | Logs, metricas e alarmes | Coleta, armazenamento e consulta de telemetria operacional |

O uso desses servicos reduz o trabalho operacional do grupo: em vez de manter manualmente control plane Kubernetes, registry, banco de dados e stack de logs, o time foca em desenvolvimento dos microservicos, pipeline e configuracao declarativa.

## Referencias para a Calculadora

Use estes itens na AWS Pricing Calculator:

1. Amazon EKS: 1 cluster em suporte padrao.
2. Amazon EC2: 2 instancias pequenas para worker nodes.
3. Amazon EBS: volumes dos worker nodes.
4. Amazon RDS for PostgreSQL: 1 instancia Single-AZ.
5. Elastic Load Balancing: 1 Application Load Balancer.
6. Amazon ECR: armazenamento das imagens.
7. Amazon CloudWatch: logs, metricas e alarmes.
8. Data Transfer: saida estimada para internet.

Links oficiais:

- [Amazon EKS pricing](https://aws.amazon.com/eks/pricing/)
- [Amazon RDS for PostgreSQL pricing](https://aws.amazon.com/rds/postgresql/pricing/)
- [Amazon EC2 On-Demand pricing](https://aws.amazon.com/ec2/pricing/on-demand/)
- [Elastic Load Balancing pricing](https://aws.amazon.com/elasticloadbalancing/pricing/)
- [Amazon ECR pricing](https://aws.amazon.com/ecr/pricing/)
- [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)
- [AWS Pricing Calculator](https://calculator.aws/)
