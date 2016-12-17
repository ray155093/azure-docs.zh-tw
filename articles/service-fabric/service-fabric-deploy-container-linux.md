---
title: "Service Fabric 以及在 Linux 中部署容器 | Microsoft Docs"
description: "Service Fabric 以及使用 Docker 容器來部署微服務應用程式。 本文說明 Service Fabric 為容器提供的功能，以及如何將 Docker 容器映像部署至叢集"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>將 Docker 容器部署至 Service Fabric
> [!div class="op_single_selector"]
> * [部署 Windows 容器](service-fabric-deploy-container.md)
> * [部署 Docker 容器](service-fabric-deploy-container-linux.md)
>
>

本文將引導您在 Linux 上的 Docker 容器中建置容器化服務。

Service Fabric 有數個容器功能可協助您建置由容器化微服務組成的應用程式。 這些稱為容器化服務。

功能包括：

* 容器映像部署和啟用
* 資源管理
* 儲存機制驗證
* 容器連接埠至主機連接埠的對應
* 容器至容器的探索及通訊
* 能夠設定環境變數

## <a name="packaging-a-docker-container-with-yeoman"></a>使用 Yeoman 封裝 Docker 容器
在 Linux 上封裝容器時，您可以選擇使用 Yeoman 範本，或是[手動建立應用程式套件](service-fabric-deploy-container.md#manually)。

Service Fabric 應用程式可以包含一或多個容器，而每個容器在提供應用程式的功能時都有特定角色。 適用於 Linux 的 Service Fabric SDK 包含 [Yeoman](http://yeoman.io/) 產生器，可讓您輕鬆建立應用程式並新增容器映像。 讓我們使用 Yeoman 來建立具有單一 Docker 容器的新應用程式 SimpleContainerApp。 您可以在稍後編輯產生的資訊清單檔案來新增更多服務。

## <a name="create-the-application"></a>建立應用程式
1. 在終端機中，輸入 **yo azuresfguest**。
2. 針對架構選擇 [容器]。
3. 為應用程式命名，例如 SimpleContainerApp
4. 從 DockerHub 儲存機制提供容器映像的 URL。 其格式為 [儲存機制]/[映像名稱]

![容器的 Service Fabric Yeoman 產生器][sf-yeoman]

## <a name="deploy-the-application"></a>部署應用程式
建置應用程式後，可以使用 Azure CLI 將它部署到本機叢集。

1. 連接到本機 Service Fabric 叢集。

    ```bash
    azure servicefabric cluster connect
    ```
2. 使用範本中所提供的安裝指令碼，將應用程式套件複製到叢集的映像存放區、註冊應用程式類型，以及建立應用程式的執行個體。

    ```bash
    ./install.sh
    ```
3. 開啟瀏覽器並瀏覽至位於 http://localhost:19080/Explorer 的 Service Fabric Explorer (如果在 Mac OS X 上使用 Vagrant，請以 VM 的私人 IP 取代 localhost)。
4. 展開 [應用程式] 節點，請注意，您的應用程式類型現在有一個項目，而另一個則是該類型的第一個執行個體。
5. 使用範本中提供的解除安裝指令碼，刪除應用程式執行個體並取消註冊應用程式類型。

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>後續步驟
* [Service Fabric 和容器的概觀](service-fabric-containers-overview.md)
* [使用 Azure CLI 與 Service Fabric 叢集互動](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


