<properties
   pageTitle="Service Fabric 專案建立後續步驟 |Microsoft Azure"
   description="本文包含一組用於 Service Fabric 的核心開發工作連結"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# 您的 Service Fabric 應用程式和後續步驟
您的 Azure Service Fabric 應用程式已經建立。本文說明您專案的建立方式和一些潛在後續步驟。

## 您的應用程式
每個新應用程式都包含一個應用程式專案。視所選擇的服務類型而定，可能會有一個或兩個額外的專案。

### 應用程式專案
應用程式專案包含：

- 一組對構成應用程式之服務的參考。

- 兩個發行設定檔 (本機和雲端)，可用來維護喜好設定，以搭配不同的環境使用，例如叢集端點的相關喜好設定，以及是否預設要執行升級部署。

- 兩個應用程式參數檔案 (本機和雲端)，可用來維護環境特定應用程式組態，例如為服務建立之分割區的數目。

- 部署指令碼，可用來從命令列部署您的應用程式，或透過自動化連續整合和部署管線中部署您的應用程式。

- 應用程式資訊清單，描述應用程式。您可以在 ApplicationPackageRoot 資料夾下方找到資訊清單。

### 無狀態服務
當您加入一個新的無狀態服務時，Visual Studio 會將一個服務專案 (包含來自 `StatelessService` 的類型) 加入至您的解決方案。此服務會使計數器中的區域變數遞增。

### 具狀態服務
當您加入一個新的具狀態服務時，Visual Studio 會將一個服務專案 (包含來自 `StatefulService` 的類型) 加入至您的解決方案。此服務會使其 `RunAsync` 方法中的計數器遞增，並將結果儲存在 `ReliableDictionary` 中。

### 動作項目服務
當您加入一個新的 Reliable Actor 時，Visual Studio 會將兩個專案加入至您的解決方案：動作項目專案和介面專案。

動作項目專案提供了一些方法，以便設定和取得動作項目的狀態中可靠地保存的計數器值。介面專案提供了其他服務可用來叫用動作項目的介面。

### 無狀態 Web API
無狀態 Web API 專案會提供基本一項 Web 服務，您可用來對外部用戶端開放您的應用程式。如需有關如何建構專案的詳細資訊，請參閱 [Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)。

### ASP.NET 核心

Service Fabric SDK 提供 ASP.NET 核心範本的相同集合，適用於獨立 ASP.NET 核心專案︰空的 [Web API][aspnet-webapi]，和 [Web 應用程式][aspnet-webapp]。

## 後續步驟
### 建立 Azure 叢集
Service Fabric SDK 提供一個用於開發和測試的本機叢集。若要在 Azure 中建立叢集，請參閱[從 Azure 入口網站設定 Service Fabric 叢集][create-cluster-in-portal]。

### 嘗試使用派對叢集部署到免費的 Azure

如果您想要嘗試在 Azure 中部署管理應用程式，而不設定自己的叢集，您可以使用免費的[派對叢集服務](http://aka.ms/tryservicefabric)。

### 將應用程式發行至 Azure
您可以直接從 Visual Studio 將應用程式發行至 Azure 叢集。若要瞭解做法，請參閱[將應用程式發行至 Azure][publish-app-to-azure]。

### 使用 Service Fabric 總管將叢集視覺化
「Service Fabric 總管」提供一個將叢集 (包括已部署的應用程式和實體配置) 視覺化的簡單方法。若要深入了解，請參閱[使用 Service Fabric Explorer 視覺化叢集][visualize-with-sfx]。

### 進行服務版本設定和升級
Service Fabric 可讓您為應用程式中的獨立服務進行獨立的版本設定和升級。若要深入了解，請參閱[進行服務版本設定和升級][app-upgrade-tutorial]。

### 使用 Visual Studio Team Services 設定持續整合
若要深入了解如何為 Service Fabric 應用程式設定持續整合程序，請參閱[使用 Visual Studio Team Services 設定持續整合][ci-with-vso]。


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html

<!---HONumber=AcomDC_0713_2016-->