### 一. 安裝/啟動 Kafka
```
docker compose -f docker-compose-kafka.yml up -d
```

---
### 二. 使用 Kafka
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
### 三. 安裝 KEDA 步驟
1. 本地加入 Helm Chart 路徑	
```
helm repo add kedacore https://kedacore.github.io/charts
```

2. 更新到 cache	(每次加入 Chart 就要套用)
```
helm repo update
```	

3. 執行Helm下載動作	
```
helm install keda kedacore/keda --namespace keda --create-namespace	
```
4. 檢查keda目前安裝啟動狀態	
```
kubectl get pods -n keda	
```

---
### 四. 使用 KEDA 步驟
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

---
### 五. 安裝Promethus + Grafana Stack 
###### (Node exporter , Alert Manager , Grafana)
1. 本地加入 Promethus + Grafana Chart 路徑	
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
<!-- helm repo add bitnami https://charts.bitnami.com/bitnami	 -->

2. 更新到 cache	(每次加入 Chart 就要套用)
```
helm repo update
```	

3. 執行 Prometheus + Grafana 下載動作	
```
helm upgrade --install prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace -f monitor-values.yml
```

4. 檢查 Prometheus + Grafana 目前安裝啟動狀態	
```
kubectl get svc -n monitoring
```

---
### 六. 使用 Prometheus + Grafana 步驟

1. 使用 kubectl get svc -n monitoring 找到 LoadBalancer POD端口 
2. Grafana網頁瀏覽 (預設-> 帳：admin , 密：admin )
```
http://localhost:<port>
```
>使用 LoadBalancer ，而不使用 ClusterIP ，可以不用再 forward 

3. 創建Dashboard [來源：Prometheus 語法]
>kube_deployment_status_replicas{deployment="demo-worker"}

---
### 統一安裝
- 安裝監控工具	
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml	
```

---
### (補充)注意指令
>查詢本地 Chart 放哪裡？	
helm env | grep HELM_CONFIG_HOME

>卸載	
helm uninstall keda -n keda	

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