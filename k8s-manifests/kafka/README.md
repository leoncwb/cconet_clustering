# 📦 Kafka Cluster (Kubernetes)

Este diretório contém os manifests para deploy do **Apache Kafka** com **Zookeeper** no Kubernetes, incluindo suporte a monitoramento com Prometheus e Grafana.

---

## 🚀 Serviços Configurados

### 🦍 Zookeeper
- StatefulSet com **3 réplicas** (alta disponibilidade).
- Headless Service para descoberta dos peers.
- Configuração dinâmica de IDs de servidor.
- Armazena metadados e coordena o cluster Kafka.

**Services:**
- `zookeeper` (headless service para os pods)
- `zookeeper-client` (porta 2181 para clientes externos)

---

### 🦊 Kafka Brokers
- StatefulSet com **3 brokers Kafka** (replicação configurada).
- Headless Service para descoberta dos brokers.
- Configuração automática dos `broker.id` via hostname.
- Suporte a replicação de tópicos e failover.

**Services:**
- `kafka` (headless service para comunicação interna entre brokers)
- `kafka-service` (ClusterIP para acesso interno)
- Suporte a **Kafka UI** para visualização e gerenciamento.

---

### 📊 Monitoramento
- **Kafka Exporter** expõe métricas em `/metrics` na porta **9308**.
- ServiceMonitor para Prometheus.
- Dashboards prontos no Grafana:
  - **Kafka Overview (ID 7589)**
  - **Kafka Exporter Overview**

**Services:**
- `kafka-exporter` (porta 9308)

---

## 📑 Pré-requisitos

- Kubernetes >= **1.20**
- `kubectl` configurado para acessar o cluster.
- Namespace dedicado (exemplo: `kafka`).
- Prometheus + Grafana instalados (via kube-prometheus-stack).

---

## ⚡ Como aplicar os manifests

```bash
# Criar namespace
kubectl create namespace kafka

# Aplicar Zookeeper
kubectl apply -n kafka -f zookeeper-config.yaml
kubectl apply -n kafka -f zookeeper-statefulset.yaml
kubectl apply -n kafka -f zookeeper-service.yaml

# Aplicar Kafka Brokers
kubectl apply -n kafka -f kafka-statefulset.yaml
kubectl apply -n kafka -f kafka-service.yaml

# Kafka Exporter
kubectl apply -n kafka -f kafka-exporter-deployment.yaml
kubectl apply -n kafka -f kafka-exporter-service.yaml
kubectl apply -n kafka -f kafka-exporter-servicemonitor.yaml

# Kafka UI (opcional)
kubectl apply -n kafka -f kafka-ui.yaml
