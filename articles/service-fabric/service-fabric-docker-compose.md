---
title: "Azure Service Fabric Docker Compose 預覽 | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，讓您能更輕鬆地使用 Service Fabric 協調現有容器。 這項支援目前只能預覽。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Service Fabric 對撰寫應用程式的支援 (預覽)

Docker 使用 [docker-compose.yml](https://docs.docker.com/compose) 檔案定義多容器應用程式。 為了讓客戶輕鬆熟悉 Docker 以協調 Service Fabric 上的現有容器，因此我們在平台上原生提供 Docker Compose 的預覽支援。 Service Fabric 可接受版本 3 以上的 `docker-compose.yml` 檔案。 此支援目前只能預覽，因此僅支援 Compose 指示詞子集。 例如，不支援應用程式升級。 但是，您一律能夠移除與部署應用程式，而非升級。  

若要使用此預覽，您必須透過入口網站安裝預覽 SDK (255.255.x.x 版)。 

> [!NOTE]
> 此功能目前只能預覽，尚未支援。

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>將 docker-compose.yml 檔案搭配 Service Fabric 使用 (預覽)

透過在 PS 中執行下列命令以從 docker-compose.yml 檔案建立 Service Fabric 應用程式：

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

RepositoryUserName 與 RepoistoryPassword 是指容器登錄使用者名稱與密碼。

如果您使用的是 Azure CLI 2.0，請執行下列命令：

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
這些命令會建立 Service Fabric 應用程式 (在上述範例中名為 `fabric:/TestContainerApp`)，此應用程式可透過 Service Fabric Explorer() 監視。 可透過 PS、Azure CLI 2.0 或 SFX 使用指定的 `ApplicationName` 執行健康情況查詢。

若要透過 PS 刪除該應用程式，請使用下列命令：

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

若要透過 Azure CLI 2.0 刪除該應用程式，請使用下列命令：

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

若要取得撰寫應用程式的狀態，請在 PS 中使用下列命令：

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

若為 Azure CLI 2.0，請使用下列命令：

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
```



## <a name="supported-compose-directives"></a>支援的撰寫指示詞

此預覽支援 Compose V3 格式的設定選項子集。 支援下列基本項目：

* 服務->部署->複本    
* 服務->部署->放置->限制式    
* 服務->部署->資源->限制 
*         -cpu-shares    
*         -memory
*         -memory-swap
* 服務->命令    
* 服務->環境
* 服務->連接埠    
* 服務->映像
* 服務->隔離 (僅適用於 windows)
* 服務->記錄->驅動程式    
* 服務->記錄->驅動程式->選項
* 磁碟區與部署->磁碟區    

必須設定叢集，才能強制執行資源限制，如 [Service Fabric 資源管理 (英文)](service-fabric-resource-governance.md) 中所述。
此預覽不支援所有其他的 Docker Compose 指示詞。 

## <a name="servicednsname-computation"></a>ServiceDnsName 計算

如果在撰寫檔案中指定的服務名稱是完整的網域名稱 (亦即包含句點 ‘.’)，則由 Service Fabric 註冊的 DNS 名稱為包含句點的 `<ServiceName>`。 如果不是，則 ApplicationName 中的每個路徑區段會變成服務 DNS 名稱中的網域標籤，其中的第一個路徑區段會變成最上層的網域標籤。 因此，如果指定的應用程式名稱是 `fabric:/SampleApp/MyComposeApp`，則 `<ServiceName>.MyComposeApp.SampleApp` 會是註冊的 DNS 名稱。

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose (執行個體定義) 與 Service Fabric 應用程式模型 (類型定義) 之間的差異

docker-compose.yml 檔案描述一組可部署的容器，包括其屬性與組態 (例如，環境變數與連接埠)。 在 docker-compose.yml 檔案中也有指定如放置限制式、資源限制和 DNS 名稱等部署參數。

[Service Fabric 應用程式模型](service-fabric-application-model.md)使用服務類型與應用程式類型，在此您會有許多類型相同的應用程式執行個體 (例如，每個客戶一個應用程式執行個體)。 這個以類型為基礎的模型支援同一個向執行階段註冊之應用程式的多個版本。 例如，客戶 A 可以有一個以類型 1.0 的 AppTypeA 具現化的應用程式，客戶 B 可以有另一個以相同類型與版本具現化的應用程式。 應用程式類型在應用程式資訊清單中定義，應用程式名稱與部署參數則在應用程式建立時指定。

此模型有彈性，而我們也正在規劃支援更簡單的以執行個體為基礎的部署類型，其中類型在資訊清單檔案中是隱含的。 在此模型中，每個應用程式會取得自己的獨立資訊清單。 我們正在透過新增對 docker-compose.yml 的支援 (這是以執行個體為基礎的部署格式) 來預覽此工作。


## <a name="next-steps"></a>後續步驟

* 參閱 [Service Fabric 應用程式模型](service-fabric-application-model.md)。

