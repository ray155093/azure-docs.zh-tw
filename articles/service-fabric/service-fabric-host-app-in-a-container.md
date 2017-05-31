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
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>將容器中的 .NET 應用程式部署到 Azure Service Fabric

本教學課程說明如何使用 Visual Studio 2017 Update 3 預覽版，將現有的 ASP.NET 應用程式部署在 Windows 容器中。 然後，說明如何使用 Visual Studio Team Services 將該容器部署到 Azure，並將該容器裝載在 Service Fabric 叢集中。

## <a name="prerequisites"></a>必要條件

1. 安裝 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上執行容器。
2. 熟悉 [Windows 10 容器快速入門][link-container-quickstart]。
3. 針對本文，我們將使用 **Fabrikam Fiber**，您可以從[這裡][link-fabrikam-github]下載此範例應用程式。
4. [Azure PowerShell][link-azure-powershell-install]
5. [Visual Studio 2017 的持續傳遞工具擴充功能][link-visualstudio-cd-extension]

>[!NOTE]
>如果這是您第一次在電腦上執行 Windows 容器映像，則 Docker CE 必須下拉適用於您容器的基底映像。 本教學課程中使用的映像大小為 14 GB。 請繼續並在 Powershell 中執行下列命令來提取基底映像：
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>將應用程式容器化

為了開始執行我們在容器中的應用程式，我們必須在 Visual Studio 的專案中新增「Docker 支援」。 當您在應用程式中新增「Docker 支援」時，會發生兩件事。 首先，會在專案中加入一個 _docker_ 檔案。 這個新檔案說明容器映像的建置方式。 其次，會在方案中加入一個新的 _docker-compose_ 專案。 這個新專案包含幾個 docker-compose 檔案，可用來說明容器的執行方式。

深入了解如何使用 [Visual Studio 容器工具][link-visualstudio-container-tools]。

### <a name="add-docker-support"></a>新增 Docker 支援

1. 在 Visual Studio 中開啟 **FabrikamFiber.CallCenter.sln** 檔案

2. 在 [FabrikamFiber.Web] 專案上按一下滑鼠右鍵 > [加入] > [Docker 支援]。

### <a name="add-support-for-sql"></a>新增對 SQL 的支援

此應用程式使用 SQL 作為資料提供者，因此必須要有SQL Server，才能執行此應用程式。 在本教學課程中，我們會使用在容器中執行的 SQL Server。
若要告訴 Docker 我們想要執行容器中的 SQL Server，我們可以在 docker-compose 專案的 docker-compose.override.yml 檔案中參考 SQL Server 容器映像。 如此一來，在 Visual Studio 中進行應用程式偵錯時，就會使用在容器中執行的 SQL Server。

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

   >[!NOTE]
   >如果您想要一律執行您在容器中的 SQL Server，您可以選擇將上述內容新增到 docker-compose.yml 檔案，而不是新增到 docker-compose.override.yml 檔案。


4. 修改 `fabrikamfiber.web` 節點，新增一個名為 `depends_on:` 的新子節點。 這可確保 `db` 服務 (SQL Server 容器) 會在我們的 Web 應用程式 (fabrikamfiber.web) 啟動之前啟動。

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
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

針對本教學課程的其餘部分，您會使用 Visual Studio Team Services 來建置及部署容器、將它推送到 Azure Container Registry，然後將它部署到在 Azure 中執行的 Service Fabric。

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集

如果您已經有 Service Fabric 叢集來作為應用程式部署目的地，便可以略過此步驟。 否則，讓我們繼續並建立 Service Fabric 叢集。

>[!NOTE]
>下列程序會建立一個受自我簽署憑證保護的 Service Fabric 叢集，該憑證放在 KeyVault 中，是在部署時所一併建立。 如需有關使用 Azure Active Directory 認證的詳細資訊，請參閱[使用 Azure Resource Manager 來建立 Service Fabric 叢集][link-servicefabric-create-secure-clusters]一文。

1. 下載下列各項中所參考 Azure 範本與參數檔的本機複本。
    * [適用於 Service Fabric 的 Azure Resource Manager][link-sf-clustertemplate] - 定義 Service Fabric 叢集的 Resource Manager 範本。
    * [範本參數檔][link-sf-clustertemplate-parameters] - 可供您自訂叢集部署的參數檔。
2. 自訂參數檔中的下列參數：
  
   | 參數       | 說明 |
   | --------------- | ----------- |
   | clusterName     | 您叢集的名稱。 |
   | adminUserName   | 叢集虛擬機器上的本機管理帳戶。 |
   | adminPassword   | 叢集虛擬機器上本機管理帳戶的密碼。 |
   | clusterLocation | 要作為叢集部署目的地的 Azure 區域。 |
   | vmInstanceCount | 您叢集中的節點數目 (可以是 1) |

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
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 參數應該配合參數檔中指定的 clusterName 參數，以及繫結至您所選擇 Azure 區域的網域，例如：`clustername.eastus.cloudapp.azure.com`。
   
    完成設定之後，它除了會將憑證複製到 CertificateOutputFolder 目錄之外，也會輸出在 Azure 中建立之叢集的相關資訊。

8. **按兩下**憑證以將它安裝在您的本機電腦上。

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
   
   完成設定之後，每當您將更新推送至存放庫時，系統都會將您的容器部署至 Service Fabric。

7. 使用 **Team Explorer** 來**啟動某個組建**，然後就可以看到您的容器應用程式在 Service Fabric 中執行。

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
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

