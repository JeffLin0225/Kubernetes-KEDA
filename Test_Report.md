# KEDA 實作報告
### 一. 事前準備
1. 寫一個 Application(.net)來做為 POD服務
2. 基於docker 安裝kafka 
3. 基於kubernetes 安裝 KEDA,Prometheus+Grafana
2.3.參考 [Installation.md](https://github.com/JeffLin0225/Kubernetes-Project/blob/main/Installation.md)

### 二. 使用服務 
1. Application 包成 image
2. 啟動kafka
3. 啟動 KEDA,Prometheus+Grafana
2.3.參考 [Usage.md](https://github.com/JeffLin0225/Kubernetes-Project/blob/main/Usage.md)
### 三. 開始實驗
1. 在 kafka 來源topic 拋送大量資料
2. 檢視terminal , Grafana 的紀錄變化

### 四. 總結
本專案成功實作了基於 KEDA (Kubernetes Event-driven Autoscaling) 的事件驅動自動擴展架構，驗證了在面對突發流量時，系統如何透過「訊息堆積量 (Lag)」而非傳統的 CPU/Memory 指標來進行更精準的擴容。

核心成果：

- 精準擴容 (Precision Scaling)： 突破了 Kubernetes 原生 HPA 的限制。證明了透過監控 Kafka Lag，可以讓 Consumer 在積壓發生前就預先反應，實現真正的「依負載擴展」。

- 混合環境整合 (Hybrid Networking)： 解決了本地開發環境 (macOS/Docker Desktop) 最棘手的網路問題，成功打通 K8s 叢集內部 (KEDA/Prometheus) 與 Host 端容器 (Kafka) 的連線 (host.docker.internal)。

- 完整可觀測性 (Full Observability)： 整合 Prometheus 與 Grafana，不僅實現了自動化，更透過視覺化儀表板即時驗證了 Lag 上升 -> HPA 觸發 -> Pod 擴展 -> Lag 下降 的完整閉環。

心得： 這不僅是一個自動擴展的實驗，更是一個關於微服務架構中「計算資源」與「訊息吞吐量」動態平衡的實踐。

>***不為了什麼，為了好玩。*** —— 透過本地環境模擬出併發處理架構，是工程師最大的浪漫。