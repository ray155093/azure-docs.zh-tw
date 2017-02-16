---
title: "Azure Government 開發人員指南| Microsoft Docs"
description: "本文針對 Azure Government 比較功能並提供應用程式開發的指引。"
services: azure-government
cloud: gov
documentationcenter: 
author: sachamicrosoft
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: e0e313f2f3a077f45ba5935363c251bba89bc524
ms.openlocfilehash: c20736554425204f47fed96f8618f66002623259


---
# <a name="azure-government-developer-guide"></a>Azure Government 開發人員指南
Azure Government 環境是與 Microsoft 其他網路隔開的實體執行個體。 本指南討論應用程式開發人員和系統管理員在與 Azure 個別區域互動及處理時需要了解的差異。

## <a name="overview"></a>概觀
Azure Government 是 Microsoft Azure 服務的獨立執行個體。 用來處理美國聯邦機構、州和地方政府及其解決方案提供者的安全性和合規性需求。 Azure Government 提供與非美國政府部署隔離的實體及網路環境，並提供遴選過的美國工作人員。

Microsoft 提供不同工具來幫助開發人員建立雲端應用程式並部署至全域的 Microsoft Azure 服務 (全域服務) 及 Microsoft Azure Government 服務。

當開發人員建立應用程式並部署至 Azure Government 服務時，和全域服務相反，開發人員必須知道這兩種服務的主要差異。 特別需要知道的是：安裝及設定其程式設計環境、設定端點、撰寫應用程式，以及將應用程式部署為 Azure Government 的服務。

本文件中的資訊將概述這些差異。 並以 [Azure Government](http://www.azure.com/gov "Azure Government") 網站及 MSDN 上 [Microsoft Azure 技術文件庫](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") 中提供的資訊做補充。 也可以在其他位置找到官方資訊，例如 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure 信任中心")、](https://azure.microsoft.com/documentation/)Azure 文件中心[、]Azure 部落格[(https://azure.microsoft.com/blog/ "Azure 部落格")。

此內容的適用對象是要對 Microsoft Azure Government 進行部署的合作夥伴和開發人員。

## <a name="guidance-for-developers"></a>開發人員指導方針
目前大部分可取得的技術內容皆假設應用程式是針對「全域服務」而不是針對 Azure Government 而開發。 基於這個理由，在開發裝載於 Azure Government 的應用程式時，務必要知道兩個重要的差異。

* 某些在「全域服務」特定區域中的服務和功能可能無法在 Azure Government 中提供。
* Azure Government 中的功能設定可能會與全域服務中的設定不同。 因此，務必檢閱您的範例程式碼、設定和步驟，以確保您是在「Azure Government 雲端服務」環境內建置和執行。

## <a name="available-features-and-services-in-azure-government"></a>Azure Government 提供的功能和服務
在「美國政府愛荷華州」(US GOV IOWA) 及「美國政府維吉尼亞州 (US GOV VIRGINIA)」區域有下列可用的 Azure Government 功能和服務：

* 虛擬機器
* 虛擬機器擴展集
* 容器服務
* Batch 帳戶
* 遠端應用程式集合
* 可用性集合
* 虛擬網路
* 負載平衡器
* 應用程式閘道
* 虛擬網路閘道
* 區域網路閘道
* 路由表
* 流量管理員設定檔
* ExpressRoute 線路
* 網路安全性群組
* 網路介面
* 公用 IP 位址
* 連線
* 儲存體帳戶
* StorSimple Manager
* App Service
* 媒體服務
* SQL Database
* SQL 資料倉儲
* SQL Server Stretch Database
* Redis 快取
* SQL Database 彈性集區
* SQL Server
* Log Analytics
* 事件中樞
* 服務匯流排命名空間
* Azure Active Directory
* Multi-Factor Authentication
* Rights Management
* 自動化帳戶
* Marketplace

另外還有其他的服務，並且會持續加入更多服務。 如需最新的服務清單，請參閱[各區域提供的產品](https://azure.microsoft.com/regions/#services)，網頁中顯示每個區域可使用的服務。

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。 如需最新的資料中心和可用服務清單，請參閱[各區域提供的產品](https://azure.microsoft.com/regions/#services)。

## <a name="endpoint-mapping"></a>端點對應
若要了解如何將公用 Azure 與 SQL Database 端點對應至 Azure Government 特定端點時，請參閱下表：

| 名稱 | Azure Government 端點 |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

如需經由 Azure Active Directory 進行 Azure Resource Manager 驗證，請參閱[驗證 Azure Resource Manager 要求](https://msdn.microsoft.com/library/azure/dn790557.aspx)。

## <a name="next-steps"></a>後續步驟
如需 Azure Government 的詳細資訊，請參閱下列資源：

* [註冊試用版](https://azuregov.microsoft.com/trial/azuregovtrial)
* [取得和存取 Azure Government](http://azure.com/gov)
* [Azure Government 概觀](/azure-government-overview)
* [Azure Government 部落格](http://blogs.msdn.com/b/azuregov/)
* [Azure 合規性](https://azure.microsoft.com/support/trust-center/compliance/)



<!--HONumber=Jan17_HO1-->


