# Ingress Configurações – Monitoramento

Este diretório contém os manifests de **Ingress** para expor os serviços de monitoramento e métricas no cluster OKE via **Ingress-NGINX**.

---

## 📂 Serviços Expostos

- **Grafana** → `http://grafana.example.local`
- **Prometheus** → `http://prometheus.example.local`
- **Kafka Exporter** → `http://kafka-exporter.example.local`
- **Redis Exporter** → `http://redis-exporter.example.local`

---

## 🚀 Deploy

1. Instale o Ingress Controller (caso não esteja instalado):

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace kube-system


Aplique os ingressos:

kubectl apply -f ingress/grafana-ingress.yaml
kubectl apply -f ingress/prometheus-ingress.yaml
kubectl apply -f ingress/kafka-exporter-ingress.yaml
kubectl apply -f ingress/redis-exporter-ingress.yaml

🔧 DNS

Você pode:

Criar registros DNS privados no OCI apontando para o IP do LoadBalancer do Ingress.

Ou, para testes, editar /etc/hosts nas suas instâncias Compute:

<LB_IP_INGRESS> grafana.example.local
<LB_IP_INGRESS> prometheus.example.local
<LB_IP_INGRESS> kafka-exporter.example.local
<LB_IP_INGRESS> redis-exporter.example.local

✅ Verificação

Após aplicar, valide:

kubectl get ingress -A


E acesse via browser ou curl:

curl http://grafana.example.local
curl http://prometheus.example.local

---
