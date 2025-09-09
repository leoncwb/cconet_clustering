# CCONET Clustering – Kubernetes Manifests

Este repositório contém manifests Kubernetes para implantação de **clusters distribuídos** com foco em **alta disponibilidade** e **escalabilidade**.  
Os serviços foram configurados em microsserviços separados, utilizando **Kubernetes Deployments + Services + ConfigMaps**.

---

## 📂 Estrutura do Projeto

k8s-manifests/
├── kafka/ # Manifests para o cluster Kafka
└── redis/ # Manifests para o cluster Redis + Sentinel

yaml
Copiar código

---

## 🚀 Serviços Configurados

### 🔴 Redis + Sentinel (Alta Disponibilidade)
- 1 Pod **Redis Master**
- 2 Pods **Redis Slaves** (replicação)
- 3 Pods **Redis Sentinel** (monitoramento e failover automático)
- Services para expor os componentes:
  - `redis-master`
  - `redis-slave`
  - `redis-sentinel`

👉 Guia detalhado: [redis/README.md](./redis/README.md)

---

### 🟡 Kafka Cluster
- Cluster Kafka configurado para rodar em Kubernetes.
- Inclui os manifests de **Brokers** e **Zookeeper**.
- Suporte para consumidores/produtores distribuídos.

👉 Guia detalhado: [kafka/README.md](./kafka/README.md)

---

## 📋 Pré-requisitos

- Kubernetes `>= 1.20`
- `kubectl` configurado no cluster
- Namespace dedicado (exemplo usado: `redis`)

---

## ▶️ Como aplicar os manifests

1. Clone este repositório:
   ```bash
   git clone https://github.com/<seu-usuario>/cconet_clustering.git
   cd cconet_clustering/k8s-manifests
Crie o namespace (se necessário):

bash
Copiar código
kubectl create namespace redis
Aplique os manifests da pasta desejada:

bash
Copiar código
kubectl apply -f redis/    # Para subir o cluster Redis + Sentinel
kubectl apply -f kafka/    # Para subir o cluster Kafka
✅ Status dos Pods
Para verificar se tudo está em execução:

bash
Copiar código
kubectl get pods -n redis -o wide
kubectl get svc -n redis
