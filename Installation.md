### 安裝KEDA
1. 本地加入 Helm Chart 路徑	
- helm repo add kedacore https://kedacore.github.io/charts

---
### 安裝Promethus + Grafana Stack (Node exporter , Alert Manager , Grafana)
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

<!-- helm repo add bitnami https://charts.bitnami.com/bitnami	 -->

2. 更新到 cache	(每次加入 Chart 就要套用)
```
helm repo update
```	

3. 執行Helm下載動作	
```
helm install keda kedacore/keda --namespace keda --create-namespace	
```
4. 檢查目前安裝啟動狀態	
```
kubectl get pods -n keda	
```

>查詢本地 Chart 放哪裡？	
helm env | grep HELM_CONFIG_HOME

5. 安裝監控工具	
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml	
```

---
### 注意指令
>卸載	
helm uninstall keda -n keda	
