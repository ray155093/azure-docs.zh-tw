<properties
   pageTitle="應用程式升級：進階主題 | Microsoft Azure"
   description="本文章涵蓋升級 Service Fabric 應用程式相關的一些進階主題。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/18/2016"
   ms.author="subramar"/>

# Service Fabric 應用程式升級：進階主題

## 在應用程式升級期間加入或移除服務

如果新服務加入已經部署的應用程式中，並發佈為升級，則新服務將會加入部署的應用程式 (不會進行升級來影響任何已在應用程式中的服務)。不過，將會為要開始使用的新服務啟動已加入的服務執行個體 (使用 `New-ServiceFabricService` Cmdlet)。

服務也可以在升級過程中從應用程式移除，不過，您必須確認 (在升級過程式移除的) 服務的所有目前執行個體都先停止 (使用 `Remove-ServiceFabricService` Cmdlet)，然後才繼續升級。

## 手動升級模式

> [AZURE.NOTE]  只有對於失敗或已暫停的升級，才應該考量不受監控手動模式。監視模式是對於 Service Fabric 應用程式的建議升級模式。

Azure Service Fabric 會提供多個升級模式以支援開發和生產叢集。每個部署選項分別適合不同的環境。

監視輪流應用程式升級是生產環境中所使用最典型的升級。指定升級原則時，Service Fabric 會確保應用程式健康狀態良好，再繼續執行升級。

 應用程式系統管理員可以使用手動輪流應用程式升級模式，對於透過各種升級網域的升級程序擁有完整控制權。對於需要更多自訂或複雜健康狀態評估原則的某些情況，或非傳統升級 (例如，應用程式已經有資料遺失)，此模式非常有用。

最後，自動輪流應用程式升級對於開發或測試環境很有用，它會在服務開發期間提供快速反覆運算週期。

## 變更為手動升級模式
**手動** - 在目前 UD 停止應用程式升級，並將升級模式變更為「未受監視的手動模式」。系統管理員需要手動呼叫 **MoveNextApplicationUpgradeDomainAsync** 以繼續進行升級，或藉由初始化新的升級來觸發回復。一旦升級進入手動模式，它會保持在手動模式中直到初始化新的升級。**GetApplicationUpgradeProgressAsync**命令會傳回 FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING。

## 使用差異封裝進行升級

Service Fabric 應用程式可以藉由佈建完整、獨立式應用程式封裝來升級。應用程式也可以使用差異封裝升級，該封裝只包含更新的應用程式檔案，和更新的應用程式資訊清單和服務資訊清單檔案。

完整的應用程式封裝包含啟動和執行 Service Fabric 應用程式所需的所有檔案。差異封裝只包含最後佈建和目前升級之間變更的檔案，以及完整的應用程式資訊清單和服務資訊清單檔案。Service Fabric 在應用程式資訊清單或服務資訊清單的建置版面配置中找不到的任何參考，Service Fabric 會在映像存放區中搜尋參考。

第一次將應用程式安裝至叢集時需要完整的應用程式封裝。後續更新可以是完整應用程式封裝或差異封裝。

有時候使用差異封裝也是很好的選擇：

* 當您有大型應用程式封裝參考數個服務資訊清單檔案及/或數個程式碼封裝、組態封裝或資料封裝時，最好使用差異封裝。

* 當您有部署系統會直接從您的應用程式建置程序產生組建版面配置時，最好使用差異封裝。在此情況下，即使程式碼中沒有任何項目變更，新建立的組件會有不同的總和檢查碼。使用完整的應用程式封裝需要您在所有的程式碼封裝上更新版本。使用差異封裝，您只需提供已變更的檔案和已變更版本的資訊清單檔案。

使用 Visual Studio 升級應用程式時，會自動發佈差異封裝。如果您想要手動建立差異封裝 (例如，為了使用 PowerShell 升級)，您應該更新應用程式和服務資訊清單，但只包含最後應用程式封裝中變更的封裝。

比方說，讓我們從下列應用程式開始 (為方便了解而提供版本號碼)︰

```text
app1       	1.0.0
  service1 	1.0.0
    code   	1.0.0
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

現在，假設您想要使用差異封裝，使用 PowerShell 只更新 service1 的程式碼封裝。現在，更新後的應用程式將會如下所示︰

```text
app1       	2.0.0      <-- new version
  service1 	2.0.0      <-- new version
    code   	2.0.0      <-- new version
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

在此情況下，您會將應用程式資訊清單更新為 2.0.0，並更新 service1 的服務資訊清單以反映程式碼封裝更新。應用程式封裝的資料夾結構會如下所示︰

```text
app1/
  service1/
    code/
```

## 後續步驟

[使用 Visual Studio 升級您的應用程式](service-fabric-application-upgrade-tutorial.md)將引導您完成使用 Visual Studio 進行應用程式升級的步驟。

[使用 PowerShell 升級您的應用程式](service-fabric-application-upgrade-tutorial-powershell.md)將引導您完成使用 PowerShell 進行應用程式升級的步驟。

使用[升級參數](service-fabric-application-upgrade-parameters.md)來控制您應用程式的升級方式。

了解如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md)，以讓您的應用程式升級相容。

參考[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)中的步驟，以修正應用程式升級中常見的問題。
 

<!---HONumber=AcomDC_0525_2016-->