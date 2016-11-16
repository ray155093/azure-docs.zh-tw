# Overview
## [什麼是 Service Fabric？](service-fabric-overview.md)
## [了解微服務](service-fabric-overview-microservices.md)
## [應用程式案例](service-fabric-application-scenarios.md)
## [架構](service-fabric-architecture.md)

# 開始使用
## 設定開發環境
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 建立您的第一個應用程式
### [Windows 上的 C# ](service-fabric-create-your-first-application-in-visual-studio.md)
### [在 Linux 上使用 Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux 上的 C](service-fabric-create-your-first-linux-application-with-csharp.md)
## [在本機叢集上部署應用程式](service-fabric-get-started-with-a-local-cluster.md)

# 作法：
## 建置應用程式
### 基本概念
#### [程式設計模型](service-fabric-choose-framework.md)
#### [應用程式模型](service-fabric-application-model.md)
#### [服務通訊](service-fabric-connect-and-communicate-with-services.md)
#### [工具](service-fabric-manage-application-in-visual-studio.md)
#### [偵錯](service-fabric-debugging-your-application.md)
#### 監視與診斷
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [設定應用程式的安全性原則](service-fabric-application-runas-security.md)
#### [針對多種環境設定您的應用程式](service-fabric-manage-multiple-environment-app-configuration.md)

### Reliable Services 應用程式
#### [概觀](service-fabric-reliable-services-introduction.md)
#### 開始使用
##### [Windows 上的 C# ](service-fabric-reliable-services-quick-start.md)
##### [在 Linux 上使用 Java](service-fabric-reliable-services-quick-start-java.md)
#### [架構](service-fabric-reliable-services-platform-architecture.md)
#### [可靠集合](service-fabric-reliable-services-reliable-collections.md)
#### [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [設定](service-fabric-reliable-services-configuration.md)
#### [Notifications](service-fabric-reliable-services-notifications.md)
#### [備份與還原](service-fabric-reliable-services-backup-restore.md)
#### [與 Reliable Services 通訊](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [服務遠端處理](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [反向 proxy](service-fabric-reverseproxy.md)
#### [進階用量](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actor 應用程式
#### [概觀](service-fabric-reliable-actors-introduction.md)
#### 開始使用
##### [Windows 上的 C# ](service-fabric-reliable-actors-get-started.md)
##### [在 Linux 上使用 Java](service-fabric-reliable-actors-get-started-java.md)
#### [架構](service-fabric-reliable-actors-platform.md)
#### [生命週期與記憶體回收](service-fabric-reliable-actors-lifecycle.md)
#### [Polymorphism](service-fabric-reliable-actors-polymorphism.md)
#### [重新進入](service-fabric-reliable-actors-reentrancy.md)
#### [計時器和提醒](service-fabric-reliable-actors-timers-reminders.md)
#### [事件](service-fabric-reliable-actors-events.md)
#### [狀態管理](service-fabric-reliable-actors-state-management.md)
#### [設定狀態供應器](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [類型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### 來賓可執行檔應用程式
#### [部署來賓可執行檔](service-fabric-deploy-existing-app.md)
#### [部署多個來賓可執行檔](service-fabric-deploy-multiple-apps.md)

### 容器應用程式
#### [概觀](service-fabric-containers-overview.md)
#### [部署 Windows 容器](service-fabric-deploy-container.md)
#### [部署 Docker 容器](service-fabric-deploy-container-linux.md)

## 從雲端服務移轉
### [比較雲端服務與 Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [移轉室 Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## 建立和管理叢集

### 基本概念
#### [概觀](service-fabric-deploy-anywhere.md)
#### [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [容量規劃](service-fabric-cluster-capacity.md)
#### [視覺化叢集](service-fabric-visualizing-your-cluster.md)
#### [連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)
#### [安全性](service-fabric-cluster-security.md)
#### [災害復原](service-fabric-disaster-recovery.md)

### Azure 上的叢集
#### 在 Azure 上建立叢集
##### [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [節點類型與 VM 擴展集](service-fabric-cluster-nodetypes.md)
#### [調整叢集](service-fabric-cluster-scale-up-down.md)
#### [升級叢集](service-fabric-cluster-upgrade.md)
#### [刪除叢集](service-fabric-cluster-delete.md)
#### [存取控制](service-fabric-cluster-security-roles.md)
#### [設定叢集](service-fabric-cluster-fabric-settings.md)
#### [免費試用 Party Cluster](http://aka.ms/tryservicefabric)

### 獨立叢集
#### [建立獨立叢集](service-fabric-cluster-creation-for-windows-server.md)
#### [調整叢集](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [升級叢集](service-fabric-cluster-upgrade-windows-server.md)
#### [保護叢集](service-fabric-windows-cluster-x509-security.md)
#### [存取控制](service-fabric-cluster-security-roles.md)
#### [設定叢集](service-fabric-cluster-manifest.md)

## 管理和組織叢集資源
### [叢集資源管理員概觀](service-fabric-cluster-resource-manager-introduction.md)
### [叢集資源管理員架構](service-fabric-cluster-resource-manager-architecture.md)
### [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
### [應用程式群組概觀](service-fabric-cluster-resource-manager-application-groups.md)
### [設定叢集資源管理員的設定](service-fabric-cluster-resource-manager-configure-services.md)
### [資源耗用計量](service-fabric-cluster-resource-manager-metrics.md)
### [使用服務親和性](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [服務放置原則](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [管理叢集](service-fabric-cluster-resource-manager-management-integration.md)
### [叢集磁碟重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [平衡叢集](service-fabric-cluster-resource-manager-balancing.md)
### [節流](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [服務動作](service-fabric-cluster-resource-manager-movement-cost.md)

## 管理應用程式生命週期
### [概觀](service-fabric-application-lifecycle.md)
### [設定連續整合](service-fabric-set-up-continuous-integration.md)
### 部署或移除應用程式
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [應用程式升級概觀](service-fabric-application-upgrade.md)
### [設定應用程式升級](service-fabric-visualstudio-configure-upgrade.md)
### [應用程式升級參數](service-fabric-application-upgrade-parameters.md)
### 升級應用程式
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)
### [應用程式升級的資料序列化](service-fabric-application-upgrade-data-serialization.md)
### [應用程式升級進階主題](service-fabric-application-upgrade-advanced.md)
### [REST 架構應用程式生命週期範例](service-fabric-rest-based-application-lifecycle-sample.md)

## 檢查應用程式與叢集健全狀況
### [監視 Service Fabric 健全狀況](service-fabric-health-introduction.md)
### [回報和檢查服務健全狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [新增自訂健全狀況報表](service-fabric-report-health.md)
### [使用系統健全狀況報表進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [檢視健全狀況報表](service-fabric-view-entities-aggregated-health.md)

## 監視與診斷
### 在本機上監視及診斷服務
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure 診斷記錄檔
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Service Fabric 應用程式追蹤](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Reliable Actors 中的診斷](service-fabric-reliable-actors-diagnostics.md)
### [可設定狀態 Reliable Services 中的診斷](service-fabric-reliable-services-diagnostics.md)
### [針對本機叢集進行疑難排解](service-fabric-troubleshoot-local-cluster-setup.md)
### [針對常見問題進行疑難排解](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## 調整應用程式
### [磁碟分割 Reliable Services](service-fabric-concepts-partitioning.md)
### [服務可用性](service-fabric-availability-services.md)
### [調整應用程式](service-fabric-concepts-scalability.md)
### [規劃應用程式容量](service-fabric-capacity-planning.md)

## 測試應用程式與服務
### [錯誤分析概觀](service-fabric-testability-overview.md)
### [測試服務對服務的通訊](service-fabric-testability-scenarios-service-communication.md)
### 模擬失敗
#### [使用受控的混亂](service-fabric-controlled-chaos.md)
#### [使用測試動作](service-fabric-testability-actions.md)
#### [工作負載期間](service-fabric-testability-workload-tests.md)
#### [藉由叫用資料遺失](service-fabric-use-data-loss-api.md)
#### [使用測試案例](service-fabric-testability-scenarios.md)
### [進行應用程式負載測試](service-fabric-vso-load-test.md)

# 參考
## [術語](service-fabric-technical-overview.md)
## [Reliable Actors](https://go.microsoft.com/fwlink/p/?linkid=833398)
## [Reliable Actors WCF](https://go.microsoft.com/fwlink/p/?linkid=833401)
## [Reliable Services](https://go.microsoft.com/fwlink/p/?linkid=833402)
## [Reliable Services WCF](https://go.microsoft.com/fwlink/p/?linkid=833403)
## [資料](https://go.microsoft.com/fwlink/p/?linkid=833404)
## [資料介面](https://go.microsoft.com/fwlink/p/?linkid=833406)
## [系統](https://go.microsoft.com/fwlink/p/?linkid=833407)
## [範例程式碼](http://aka.ms/servicefabricsamples)
## [PowerShell](/powershell/servicefabric/vlatest/servicefabric)
## [Java API](https://go.microsoft.com/fwlink/p/?linkid=833410)
## [.NET](/dotnet/api)
## [REST](/rest/api/servicefabric/)

# 資源
## [學習路徑](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [價格](https://azure.microsoft.com/pricing/details/service-fabric/)
## [服務更新](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Nov16_HO2-->


