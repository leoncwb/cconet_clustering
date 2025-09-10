Redis Cluster + Sentinel no Kubernetes
📌 Arquitetura

1 Pod Redis Master

2 Pods Redis Slaves (replicação)

3 Pods Redis Sentinel (failover automático)

Redis Exporter para métricas Prometheus

Services:

redis-master (ClusterIP interno, usado pelos slaves e sentinels)

redis-slave (ClusterIP interno, para replicação)

redis-sentinel (ClusterIP interno, health/failover)

redis-lb (LoadBalancer, acesso externo para integrações com instâncias Compute)

🚀 Deploy

Aplicar os manifests:

kubectl apply -f redis/


Validar pods e services:

kubectl get pods -n redis -o wide
kubectl get svc -n redis

🔗 Integração com OKE + Compute

Dentro do OKE, os pods usam os serviços ClusterIP.

Para instâncias Compute (Jenkins, PHP App, etc.), utilize o Service LoadBalancer:

kubectl get svc redis-lb -n redis


Exemplo de teste:

redis-cli -h <LB_IP_PRIVADO_REDIS> -p 6379 ping


🔒 Lembre-se de liberar a porta 6379 no NSG/Security List da VCN.

📊 Monitoramento

O Redis Exporter está configurado e exposto via Service + ServiceMonitor.
As métricas podem ser acessadas pelo Prometheus e visualizadas em dashboards do Grafana.
