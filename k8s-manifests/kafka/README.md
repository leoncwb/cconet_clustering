Kafka Cluster no Kubernetes
📌 Arquitetura

3 Pods Zookeeper

3 Pods Kafka Broker

Kafka Exporter + JMX Exporter para métricas Prometheus

Services:

zookeeper (ClusterIP interno)

kafka (ClusterIP interno para comunicação dentro do cluster)

kafka-lb (LoadBalancer, acesso externo para integrações com instâncias Compute)

kafka-exporter (para métricas Prometheus)

🚀 Deploy

Aplicar os manifests:

kubectl apply -f kafka/


Validar pods e services:

kubectl get pods -n kafka -o wide
kubectl get svc -n kafka

🔗 Integração com OKE + Compute

Dentro do OKE, os pods e aplicações usam o ClusterIP (kafka service).

Para instâncias Compute (Jenkins, PHP App, etc.), utilize o Service LoadBalancer:

kubectl get svc kafka-lb -n kafka


⚠️ Importante: no Kafka, o parâmetro advertised.listeners deve apontar para o IP do LoadBalancer, por exemplo:

listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://<LB_IP_PRIVADO_KAFKA>:9092


Exemplo de teste:

# Producer
kafka-console-producer.sh --broker-list <LB_IP_PRIVADO_KAFKA>:9092 --topic test

# Consumer
kafka-console-consumer.sh --bootstrap-server <LB_IP_PRIVADO_KAFKA>:9092 --topic test --from-beginning


🔒 Lembre-se de liberar a porta 9092 no NSG/Security List da VCN.

📊 Monitoramento

Kafka Exporter expõe métricas em /metrics.

Coletado pelo Prometheus via ServiceMonitor.

Dashboards recomendados:

Kafka Overview

Kafka Exporter Overview
