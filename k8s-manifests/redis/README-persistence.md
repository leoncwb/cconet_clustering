# Redis Cluster – Persistência de Dados no Kubernetes

Por padrão, os manifests anteriores usavam `emptyDir`, o que significa que os dados eram **apagados** sempre que o Pod era reiniciado.  
Este documento descreve como adicionar **PersistentVolumes (PV)** e **PersistentVolumeClaims (PVC)** para garantir persistência dos dados no Redis Master e Slaves.

---

## 📂 Estrutura de Arquivos



k8s-manifests/redis/
├── redis-master-pv.yaml
├── redis-master.yaml
├── redis-slave1-pv.yaml
├── redis-slave1.yaml
├── redis-slave2-pv.yaml
└── redis-slave2.yaml


---

## 🚀 Passos para Configurar

### 1. Criar os PersistentVolumes e PVCs

Cada nó terá seu diretório dedicado (`hostPath`):
- Master → `/data/redis/master`
- Slave1 → `/data/redis/slave1`
- Slave2 → `/data/redis/slave2`

Aplicar os manifests:

```bash
kubectl apply -f redis-master-pv.yaml
kubectl apply -f redis-slave1-pv.yaml
kubectl apply -f redis-slave2-pv.yaml


Verificar:

kubectl get pv -n redis
kubectl get pvc -n redis

2. Atualizar os Deployments

Os deployments foram alterados para usar:

volumes:
- name: redis-master-data
  persistentVolumeClaim:
    claimName: redis-master-pvc


E cada container monta em /data:

volumeMounts:
- mountPath: /data
  name: redis-master-data


Isso garante que os dados sejam gravados no disco persistente.

3. Aplicar os Deployments

Atualizar os deployments do Master e Slaves:

kubectl apply -f redis-master.yaml
kubectl apply -f redis-slave1.yaml
kubectl apply -f redis-slave2.yaml

4. Validar Persistência

Entrar no Master e gravar uma chave:

kubectl exec -it -n redis deploy/redis-master -- \
  redis-cli SET teste "persistencia-ok"


Ler a chave de um Slave:

kubectl exec -it -n redis deploy/redis-slave1 -- \
  redis-cli GET teste


Deletar o Pod Master:

kubectl delete pod -n redis -l app=redis-master


Quando o Pod voltar, verificar se a chave ainda existe:

kubectl exec -it -n redis deploy/redis-master -- \
  redis-cli GET teste

📊 Resultado Esperado

Os Pods podem ser reiniciados sem perda de dados.

O cluster mantém consistência mesmo após falhas.

Cada Slave continua replicando do Master.
---
