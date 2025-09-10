CCONET Clustering – Kubernetes Manifests

Este repositório contém manifests Kubernetes para implantação de clusters distribuídos com foco em alta disponibilidade, escalabilidade e observabilidade.
Os serviços foram configurados em microsserviços separados, utilizando Kubernetes StatefulSets/Deployments + Services + ConfigMaps + ServiceMonitor.

📂 Estrutura do Projeto
k8s-manifests/
├── kafka/   # Manifests para o cluster Kafka
├── redis/   # Manifests para o cluster Redis + Sentinel
└── ingress/ # Ingress resources para Grafana, Prometheus e Exporters

🚀 Serviços Configurados
🔴 Redis + Sentinel (Alta Disponibilidade)

1 Pod Redis Master

2 Pods Redis Slaves (replicação)

3 Pods Redis Sentinel (monitoramento e failover automático)

Exporter para métricas Prometheus

Services configurados:

redis-master (interno – ClusterIP)

redis-slave (interno – ClusterIP)

redis-sentinel (interno – ClusterIP)

redis-lb (externo – LoadBalancer para integração com instâncias Compute)

👉 Guia detalhado: redis/README.md

🟡 Kafka Cluster

Cluster Kafka configurado para rodar em Kubernetes (StatefulSet).

Inclui os manifests de Brokers e Zookeeper.

Suporte para consumidores/produtores distribuídos.

Exporter para métricas Prometheus.

Services configurados:

kafka (interno – ClusterIP)

kafka-lb (externo – LoadBalancer para integração com instâncias Compute)

zookeeper (interno – ClusterIP)

👉 Guia detalhado: kafka/README.md

📊 Monitoramento e Observabilidade

Prometheus Operator (kube-prometheus-stack)

Redis Exporter e Kafka Exporter + JMX Exporter

Grafana com dashboards importados:

Kafka Overview

Kafka Exporter Overview

Redis Overview

🌐 Ingress Controller

Ingress-NGINX instalado via Helm

Serviços HTTP expostos por Ingress:

grafana.example.local → Grafana

prometheus.example.local → Prometheus

kafka-exporter.example.local → Kafka Exporter

redis-exporter.example.local → Redis Exporter

📋 Pré-requisitos

Kubernetes >= 1.20

kubectl configurado no cluster

Namespace dedicado (exemplos: redis, kafka, monitoring)

OKE (Oracle Kubernetes Engine) configurado na mesma VCN/Subnet das instâncias Compute para integração via LoadBalancer

▶️ Como aplicar os manifests

Clone este repositório:

git clone https://github.com/<seu-usuario>/cconet_clustering.git
cd cconet_clustering/k8s-manifests


Crie os namespaces (se necessário):

kubectl create namespace redis
kubectl create namespace kafka
kubectl create namespace monitoring


Suba o cluster Redis + Sentinel:

kubectl apply -f redis/


Suba o cluster Kafka:

kubectl apply -f kafka/


Aplique os ingressos (monitoramento e exporters):

kubectl apply -f ingress/

✅ Validação e Testes
Verificar pods e services
kubectl get pods -n redis -o wide
kubectl get svc -n redis
kubectl get pods -n kafka -o wide
kubectl get svc -n kafka

Testar Redis
redis-cli -h <LB_IP_PRIVADO_REDIS> -p 6379 ping

Testar Kafka
# Producer
kafka-console-producer.sh --broker-list <LB_IP_PRIVADO_KAFKA>:9092 --topic test

# Consumer
kafka-console-consumer.sh --bootstrap-server <LB_IP_PRIVADO_KAFKA>:9092 --topic test --from-beginning

Acessar Grafana
http://grafana.example.local

🔒 Segurança em Rede (OKE + Compute)

Redis: Porta 6379 liberada para Subnet das Compute Instances

Kafka: Porta 9092 liberada para Subnet das Compute Instances

Ingress: Porta 80/443 liberada conforme necessidade

Configuração feita via NSG/Security List no OCI.

