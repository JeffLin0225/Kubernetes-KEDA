### 一. 安裝/啟動 Kafka
docker compose -f docker-compose-kafka.yml up -d

>*檢查是否啟動無誤*

---
### 二. 使用 KEDA 步驟
1. 先打包 Application	
docker build -t keda-worker:v1 .

2. 安裝 appliction → k8s 	
kubectl apply -f deployment-keda.yaml

3. 監控查詢 KEDA 事件	
kubectl get scaledobject -o wide 
kubectl get hpa -o wide
kubectl get pods -w 	

>*預設 pod 數量，應該為 0*

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