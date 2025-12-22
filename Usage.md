### 一. 使用 Kafka
1. 查看 Topic
```
docker exec -it kafka bash
```
2. 創建 Topic
```
kafka-topics --bootstrap-server localhost:9092 --create --topic source-topic --partitions 3 --replication-factor 1
kafka-topics --bootstrap-server localhost:9092 --create --topic router-topic --partitions 3 --replication-factor 1
```
3. 查看 Topic
```
kafka-topics --bootstrap-server localhost:9092 --list
```
4. 聽 Topic
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic router-topic --from-beginning
```

---
### 二. 使用 KEDA 步驟
1. 先打包實作拋送kafka的 Application	
```
docker build -t keda-worker:v1 .
```
2. 安裝 appliction → k8s 	
```
kubectl apply -f deployment-keda.yaml
```
3. 監控查詢 KEDA 事件	
```
kubectl get scaledobject -o wide 
kubectl get hpa -o wide
kubectl get pods -w 	
```
>*預設 pod 數量，應該為 0*

### 三. 使用 Prometheus + Grafana 步驟

1. 使用 kubectl get svc -n monitoring 找到 LoadBalancer POD端口 
2. Grafana網頁瀏覽 (預設-> 帳：admin , 密：admin )
```
http://localhost:<port>
```
>使用 LoadBalancer ，而不使用 ClusterIP ，可以不用再 forward 
---
### (補充)注意指令

>*卸除 Kafka 容器*
docker compose -f docker-compose-kafka.yml down -v

>*卸除 keda 容器*
kubectl delete -f deployment-keda.yaml

>*強制殺掉 application pod*	
kubectl scale deployment demo-worker --replicas=0	

>*強制砍掉 KEDA pod （重啟大腦）*
kubectl delete pod -n keda -l app=keda-operator

>*刪掉記得看一下*
kubectl get pods -n keda

>*（補充）監控資源用量*
kubectl top nodes
kubectl top pods	