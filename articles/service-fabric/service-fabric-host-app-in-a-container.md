---
title: "將容器中的 .NET 應用程式部署到 Azure Service Fabric | Microsoft Docs"
description: "教導您如何在 Visual Studio 中以 Docker 容器封裝 .NET 應用程式。 然後，將這個新的「容器」應用程式部署到 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: zh-tw
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>將容器中的 .NET 應用程式部署到 Azure Service Fabric

本教學課程說明如何使用 Visual Studio 2017 Update 3 預覽版，將現有的 ASP.NET 應用程式部署在 Windows 容器中。 然後，說明如何使用 Visual Studio Team Services 將該容器部署到 Azure，並將該容器裝載在 Service Fabric 叢集中。

## <a name="prerequisites"></a>必要條件

1. 安裝 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上執行容器。
2. 熟悉 [Windows 10 容器快速入門][link-container-quickstart]。
3. 針對本教學課程內容，我們將使用 **Fabrikam Fiber CallCenter**，您可以從[這裡][link-fabrikam-github]下載範例應用程式。
4. [Azure PowerShell][link-azure-powershell-install]
5. [Visual Studio 2017 的持續傳遞工具擴充功能][link-visualstudio-cd-extension]
6. [Azure 訂用帳戶][link-azure-subscription]和 [Visual Studio Team Services 帳戶][link-vsts-account]。 您可以使用所有服務的免費層來瀏覽本教學課程。

>[!NOTE]
>如果您第一次在電腦上執行 Windows 容器映像，則 Docker CE 必須下拉適用於您容器的基底映像。 本教學課程中使用的映像為 14 GB。 請繼續並在 Powershell 中執行下列命令來提取基底映像：
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>將應用程式容器化

為了開始執行我們在容器中的應用程式，我們必須在 Visual Studio 的專案中新增「Docker 支援」。 當您在應用程式中新增「Docker 支援」時，會發生兩件事。 首先，會在專案中加入一個 _docker_ 檔案。 這個新檔案說明容器映像的建置方式。 其次，會在方案中加入一個新的 _docker-compose_ 專案。 這個新專案包含幾個 docker-compose 檔案， 可用來說明容器的執行方式。

深入了解如何使用 [Visual Studio 容器工具][link-visualstudio-container-tools]。

### <a name="add-docker-support"></a>新增 Docker 支援

1. 在 Visual Studio 中開啟 **FabrikamFiber.CallCenter.sln** 檔案

2. 在 [FabrikamFiber.Web] 專案上按一下滑鼠右鍵 > [加入] > [Docker 支援]。

### <a name="add-support-for-sql"></a>新增對 SQL 的支援

此應用程式使用 SQL 作為資料提供者，因此必須要有SQL Server，才能執行此應用程式。 在本教學課程中，我們會使用在容器中執行的 SQL Server 來進行本機偵錯。
若要在偵錯時執行容器中的 SQL Server，我們可以參考 docker-compose.override.yml 檔案中的 SQL Server 容器映像。 

1. 開啟 [方案總管]。

2. 開啟 [docker-compose] > [docker-compose.yml] > [docker-compose.override.yml]。

3. 在 `services:` 節點底下，新增一個名為 `db:` 的新節點。 這個節點會宣告在容器中執行 SQL Server。

   ```yml
     db:
       image: microsoft/mssql-server-windows-developer
       environment:
         sa_password: "Password1"
         ACCEPT_EULA: "Y"
       ports:
         - "1433"
       healthcheck:
         test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
         interval: 1s
         retries: 20
   ```

   >[!NOTE]
   >您可以使用任何慣用的 SQL Server 來進行本機偵錯，只要能夠從您的主機連線到該 SQL Server 即可。 不過，**localdb** 不支援 `container -> host` 通訊。

   >[!WARNING]
   >執行容器中的 SQL Server 時，不支援在容器停止時保存資料。 因此，請不要將這個設定用於生產環境。

4. 修改 `fabrikamfiber.web` 節點，新增一個名為 `depends_on:` 的新子節點。 這可確保 `db` 服務 (SQL Server 容器) 會在我們的 Web 應用程式 (fabrikamfiber.web) 啟動之前啟動。

   ```yml
     fabrikamfiber.web:
       depends_on:
         - db
   ```

5. 在 **FabrikamFiber.Web** 專案中，更新 **web.config** 檔案中的連接字串以指向容器中的 SQL Server。

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >在建置 Web 應用程式的發行組建時，如果您想要使用不同的 SQL Server，請在您的 web.release.config 檔案中新增另一個連接字串。

6. 按 **F5** 以執行您容器中的應用程式並進行偵錯。

   Edge 會使用容器在內部 NAT 網路上的 IP 位址 (通常是 172.x.x.x) 來開啟您應用程式的已定義啟動頁面。 若要深入了解如何使用 Visual Studio 2017 對容器中的應用程式進行偵錯，請參閱[這篇文章][link-debug-container]。

   ![容器中 fabrikam 的範例][image-web-preview]

此應用程式現在已準備就緒，可建置及封裝在容器中。 在您的電腦上建置完容器映像之後，您便可以將它推送到任何容器登錄，然後下拉到任何主機來執行。

針對本教學課程的其餘部分，您會使用 Visual Studio Team Services，將容器部署到執行於 Azure 中的 Service Fabric。

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集

如果您已經有 Service Fabric 叢集來作為應用程式部署目的地，便可以略過此步驟。 否則，讓我們繼續並建立 Service Fabric 叢集。

>[!NOTE]
>下列程序會建立單一節點 (單一虛擬機器) 預覽 Service Fabric 叢集，並由 KeyVault 中的自我簽署憑證所保護。
>調整單一節點叢集的規模時，不可以超過一部虛擬機器，而且預覽叢集無法升級至較新的版本。
>若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機][link-azure-pricing-calculator]。
>如需建立 Service Fabric 叢集的詳細資訊，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集][link-servicefabric-create-secure-clusters]一文。

1. 從這個 GitHub 存放庫，下載 Azure Resource Manager 範本和參數檔案的本機複本：
    * [適用於 Service Fabric 的 Azure Resource Manager 範本][link-sf-clustertemplate]
       - **azuredeploy.json** - 可定義 Service Fabric 叢集的 Azure Resource Manager 範本。
       - **azuredeploy.parameters.json** - 可供您自訂叢集部署的參數檔。
2. 自訂參數檔中的下列參數：
  
   | 參數       | 說明 | 建議的值 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 要作為叢集部署目的地的 Azure 區域。 | *例如 westeurope、eastasia、eastus* |
   | clusterName     | 您叢集的名稱。 | *例如 bobs-sfpreviewcluster* |
   | adminUserName   | 叢集虛擬機器上的本機管理帳戶。 | *任何有效的 Windows Server 使用者名稱* |
   | adminPassword   | 叢集虛擬機器上本機管理帳戶的密碼。 | *任何有效的 Windows Server 密碼* |
   | clusterCodeVersion | 要執行的 Service Fabric 版本。 (255.255.X.255 是預覽版本)。 | **255.255.5713.255** |
   | vmInstanceCount | 叢集中的虛擬機器數目 (可以是 1 或 3-99)。 | **1** |

3. 開啟 **PowerShell**。
4. **登入** Azure。

   ```powershell
   Login-AzureRmAccount
   ```

5. 選取要作為叢集部署目的地的**訂用帳戶**。

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. 為 Service Fabric 使用的憑證建立並**加密密碼**。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. 執行下列命令來**建立叢集**：

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 參數應該配合參數檔中指定的 clusterName 參數，以及繫結至您所選擇 Azure 區域的網域，例如：`clustername.eastus.cloudapp.azure.com`。
   
    完成設定之後，它除了會將憑證複製到 CertificateOutputFolder 目錄之外，也會輸出在 Azure 中建立之叢集的相關資訊。

8. 按兩下憑證，即可開啟 [憑證匯入精靈]。

9. 請使用預設設定，但務必勾選 [將此金鑰標記為可匯出] 核取方塊 (在「私密金鑰保護」步驟中)。 設定 Azure Container Registry 時，Visual Studio 必須匯出憑證，以進行本教學課程稍後的 Service Fabric 叢集驗證。

10. 現在，您可以在瀏覽器中開啟 Service Fabric Explorer。 URL 為 PowerShell Cmdlet 輸出中的 **ManagementEndpoint**，例如 *https://mycluster.westeurope.cloudapp.azure.com:19080*。 

>[!NOTE]
>由於您使用自我簽署的憑證，因此開啟 Service Fabric Explorer 時會顯示憑證錯誤。 在 Edge 中，您必須依序按一下 [詳細資料] 與 [繼續瀏覽網頁] 連結。 在 Chrome 中，您必須依序按一下 [進階] 與 [繼續] 連結。

>[!NOTE]
>如果叢集建立失敗，您隨時可以重新執行命令，藉此更新已部署的資源。 如果憑證是與失敗的部署一起建立，則會產生一個新憑證。 若要對叢集建立問題進行疑難排解，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集][link-servicefabric-create-secure-clusters]一文。

## <a name="getting-the-application-ready-for-the-cloud"></a>準備在雲端中使用應用程式

若要準備在 Azure 的 Service Fabric 中執行應用程式，必須完成下列兩個步驟：

1. 公開連接埠，以便透過該連接埠連接至 Service Fabric 叢集中的 Web 應用程式
2. 提供適用於應用程式且準備好用於生產環境的 SQL 資料庫

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1.在 Service Fabric 中公開 Web 應用程式
根據預設，Azure Load Balancer 會為我們設定好的 Service Fabric 叢集開啟連接埠 80，以平衡叢集的傳入流量。 透過 docker-compose.yml 檔案，我們可以在此連接埠上公開容器。

1. 開啟 [方案總管]。

2. 開啟 **docker-compose** > **docker-compose.yml**。

3. 修改 `fabrikamfiber.web` 節點，新增一個名為 `ports:` 的新子節點和 `- "80:80"` 字串。 完整的 docker-compose.yml 檔案看起來應該如下所示：

   ```yml
      version: '3'

      services:
        fabrikamfiber.web:
          image: fabrikamfiber.web
          build:
            context: .\FabrikamFiber.Web
            dockerfile: Dockerfile
          ports:
            - "80:80"
   ```

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2.提供適用於應用程式且準備好用於生產環境的 SQL 資料庫
當我們將這個應用程式容器化，並啟用本機偵錯時，即已將其設定好，可在容器中執行 SQL Server。 由於我們不需要保存資料庫中的資料，因此進行應用程式的本機偵錯時，這個方法是很好的解決方案。 不過，在生產環境中執行時，我們需要將資料保存在資料庫中。 目前無法保證資料能保存在容器中，因此請不要將 SQL Server 生產環境的資料儲存在容器中。

因此，如果您的服務需要永續性的 SQL Database，建議您利用 Azure SQL Database。 若要在 Azure 中設定並執行受管理的 SQL Server，請參閱 [Azure SQL Database 快速入門][[link-azure-sql]] 一文。

>[!NOTE]
>記得將 FabrikamFiber.Web 專案中 web.release.config 檔案的連接字串變更為 SQL Server。
>如果無法連線到任何 SQL 資料庫，此應用程式會依正常程序失敗。 您可以選擇在沒有 SQL Server 的情況下繼續部署應用程式。

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署

若要使用 Visual Studio Team Services 來設定部署，您必須安裝 [Visual Studio 2017 的持續傳遞工具擴充功能][link-visualstudio-cd-extension]。 此擴充功能可讓您透過設定 Visual Studio Team Services 來輕鬆部署至 Azure，並將您的應用程式部署至 Service Fabric 叢集。

若要開始進行，您的程式碼必須裝載在原始檔控制中。 本節的其餘部分會假設使用 **git**。

1. 在 Visual Studio 右下角，按一下 [加入至原始檔控制] > [Git] (或您慣用的任何一個選項)。

   ![按原始檔控制按鈕][image-source-control]

2. 在 [Team Explorer] 窗格中，按 [發行 Git 儲存機制]。

3. 選取您的 VSTS 存放庫名稱，然後按 [存放庫]。

   ![將儲存機制發行至 VSTS][image-publish-repo]

既然您的程式碼已與 VSTS 來源存放庫同步，現在您就可以設定持續整合和持續傳遞。

1. 在 [方案總管] 中，於 [方案] 上按一下滑鼠右鍵 > [設定持續傳遞]。

2. 選取 Azure 訂用帳戶。

3. 將 [主機類型] 設定為 [Service Fabric 叢集]。

   >[!NOTE]
   >視您要建置的容器類型而定，我們正新增更多選項供您將應用程式裝載在 Azure 中的容器內。 

4. 將 [目標主機] 設定為您在上一節中建立的 Service Fabric 叢集。

5. 選擇一個作為您容器發行目的地的**容器登錄**。

   >[!TIP]
   >使用 [編輯] 按鈕來建立容器登錄。
    
6. 按 [確定]。

   ![設定 Service Fabric 持續整合][image-setup-ci]
   
   完成設定之後，您的容器即會部署到 Service Fabric。 每當您將更新推送至存放庫時，就會執行新的組建與版本。
   
   >[!NOTE]
   >建立容器映像約需 15 分鐘。
   >Service Fabric 叢集的第一個部署會引發下載基底 Windows Server Core 容器映像的動作。 此下載還需要額外的 5-10 分鐘才能完成。

7. 使用您的叢集 URL 瀏覽至 Fabrikam Call Center 應用程式，例如 *http://mycluster.westeurope.cloudapp.azure.com*。

既然您已將 Fabrikam Call Center 方案容器化並進行部署，現在您就可以開啟 [Azure 入口網站][link-azure-portal]並看到應用程式在 Service Fabric 中執行。 若要試試應用程式，請開啟網頁瀏覽器並移至您 Service Fabric 叢集的 URL。

## <a name="next-steps"></a>後續步驟

- [Visual Studio 中的容器工具][link-visualstudio-container-tools]
- [開始在 Service Fabric 中使用容器][link-servicefabric-containers]
- [建立 Service Fabric 應用程式][link-servicefabric-createapp]

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
