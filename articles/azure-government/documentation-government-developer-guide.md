---
title: "Azure Government 開發人員指南| Microsoft Docs"
description: "本文針對 Azure Government 比較功能並提供應用程式開發的指引。"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd
ms.lasthandoff: 02/21/2017


---
# <a name="azure-government-developer-guide"></a>Azure Government 開發人員指南
Azure Government 環境是與 Microsoft 其他網路隔開的實體執行個體。 本指南討論應用程式開發人員和系統管理員在與 Azure 個別區域互動及處理時需要了解的差異。

## <a name="overview"></a>概觀
Azure Government 是 Microsoft Azure 服務的獨立執行個體。 用來處理美國聯邦機構、州和地方政府及其解決方案提供者的安全性和合規性需求。 Azure Government 提供與非美國政府部署隔離的實體及網路環境，並提供遴選過的美國工作人員。

Microsoft 提供不同工具來幫助開發人員建立雲端應用程式並部署至全域的 Microsoft Azure 服務 (全域服務) 及 Microsoft Azure Government 服務。

當開發人員建立應用程式並部署至 Azure Government 服務時，和全域服務相反，開發人員必須知道這兩種服務的主要差異。 特別需要知道的是：安裝及設定其程式設計環境、設定端點、撰寫應用程式，以及將應用程式部署為 Azure Government 的服務。

本文件中的資訊將概述這些差異。 並以 [Azure Government](http://www.azure.com/gov "Azure Government") 網站及 MSDN 上 [Microsoft Azure 技術文件庫](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") 中提供的資訊做補充。 也可以在其他位置找到官方資訊，例如 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure 信任中心")、[Azure 文件中心](https://azure.microsoft.com/documentation/)、[Azure 部落格](https://azure.microsoft.com/blog/ "Azure 部落格")。

此內容的適用對象是要對 Microsoft Azure Government 進行部署的合作夥伴和開發人員。

## <a name="guidance-for-developers"></a>開發人員指導方針
目前大部分可取得的技術內容皆假設應用程式是針對「全域服務」而不是針對 Azure Government 而開發。 基於這個理由，在開發裝載於 Azure Government 的應用程式時，務必要知道兩個重要的差異。

* 某些在「全域服務」特定區域中的服務和功能可能無法在 Azure Government 中提供。
* Azure Government 中的功能設定可能會與全域服務中的設定不同。 因此，務必檢閱您的範例程式碼、設定和步驟，以確保您是在「Azure Government 雲端服務」環境內建置和執行。

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。 如需最新的資料中心和可用服務清單，請參閱[各區域提供的產品](https://azure.microsoft.com/regions/services)。


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



## <a name="next-steps"></a>後續步驟
如需 Azure Government 的詳細資訊，請參閱下列資源：

* [註冊試用版](https://azuregov.microsoft.com/trial/azuregovtrial)
* [取得和存取 Azure Government](http://azure.com/gov)
* [Azure Government 概觀](/azure-government-overview)
* [Azure Government 部落格](http://blogs.msdn.microsoft.com/azuregov/)
* [Azure 合規性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

