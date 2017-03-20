---
title: "將記錄從 Azure 資源整合到 SIEM 系統 | Microsoft Docs"
description: "了解 Azure 記錄整合、其主要功能及運作方式。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 6859f2f0c627c9c23703875d04b4893b58775a26
ms.lasthandoff: 03/04/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 記錄整合簡介
了解 Azure 記錄整合、其主要功能及運作方式。

## <a name="overview"></a>Overview
裝載於 Azure 中的「平台即服務」(PaaS) 和「基礎結構即服務」(IaaS) 會在安全性記錄檔中產生大量資料。 這些記錄檔包含重要的資訊，可提供關於下列項目有智慧且有力的深入了解：違反原則、內部和外部威脅、法規遵循問題，以及網路、主機及使用者活動中的異常。

Azure 記錄整合可讓您將來自 Azure 資源的未經處理記錄，整合到內部部署安全性資訊及事件管理 (SIEM) 系統內。 Azure 記錄整合會從您的 Windows (WAD) 虛擬機器、Azure 活動記錄檔，Azure 資訊安全中心警示和 Azure 資源提供者記錄檔收集 Azure 診斷。 這項整合提供您內部部署或在雲端中所有資產統一的儀表板，以便您彙總、相互關聯、分析和警示安全性事件。

![Azure 記錄整合][1]

## <a name="what-logs-can-i-integrate"></a>可以整合哪些記錄檔？
Azure 會為每項 Azure 服務產生大量記錄。 這些記錄檔會分類為這幾種主要類型︰

* **控制/管理記錄檔**：可讓您看到 Azure Resource Manager 的 CREATE、UPDATE 和 DELETE 作業。 Azure 活動記錄是本類型的一個範例。
* **資料平面記錄檔**：可讓您看到使用 Azure 資源時所引發的事件。 本類型的範例是虛擬機器中的 Windows 事件系統記錄檔、安全性和應用程式記錄檔，以及透過 Azure 監視器設定的診斷記錄
* **已處理的事件**，提供代替您處理的已分析事件/警示。此類型的範例是 Azure 資訊安全中心警示，Azure 資訊安全中心已在該處處理和分析您的訂用帳戶，並提供簡要的安全性警示

Azure 記錄整合目前支援整合 Azure 活動記錄、您的 Azure 訂用帳戶中 Windows 虛擬機器的 Windows 事件記錄檔、Azure 資訊安全中心警示、Azure 診斷記錄及 Azure Active Directory 稽核記錄。

下表說明記錄檔分類和 SIEM 整合詳細資訊

| 記錄類型  |支援 JSON 的記錄分析 (Splunk、ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  AAD 稽核記錄 |  yes | 需要建立 FlexConnector JSON 剖析器檔案。 如需詳細資訊，請參閱 ArcSight 文件  |  您必須建立記錄來源擴充。 如需詳細資訊，請參閱 QRadar文件。 |  
| 活動記錄檔  | 是  |  下載中心提供 FlexConnector JSON 剖析器檔案以及 Azure 記錄整合下載 |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (透過 Syslog 傳送) |  
| ASC 警示  | 是  |  需要建立 FlexConnector JSON 剖析器檔案。 如需詳細資訊，請參閱 ArcSight 文件。 | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (透過 Syslog 傳送)   |   
| 診斷記錄 (資源記錄) | 是 | 需要使用者建立 FlexConnector JSON 剖析器檔案。 如需如何進行的指示，請參閱 ArcSight 文件。 | 您必須建立記錄來源擴充。 如需詳細資訊，請參閱 QRadar 文件 |
| VM 記錄 | 是，透過轉寄的事件，而非透過 JSON | 是，透過轉寄的事件 | 是，透過轉寄的事件 |


如果您有關於「Azure 記錄整合」的問題，請傳送電子郵件給 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>後續步驟
在本文中為您介紹了 Azure 記錄整合。 若要深入了解 Azure 記錄整合和支援的記錄檔的類型，請參閱下列各項︰

* [Azure 記錄檔的 Microsoft Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 記錄整合詳細資料、系統需求和安裝指示的下載中心。
* [開始使用 Azure 記錄整合](security-azure-log-integration-get-started.md) - 本教學課程將逐步引導您安裝 Azure 記錄整合，和來自 Azure WAD 儲存體、Azure 活動記錄、Azure 資訊安全中心警示以及 Azure Active Directory 稽核記錄檔。
* [整合診斷記錄 (英文)](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) – 此部落格文章提供使用 Azure 記錄整合來整合診斷記錄的步驟
* [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 此部落格文章說明如何設定 Azure 記錄整合，以搭配使用合作夥伴解決方案 Splunk、HP ArcSight 和 IBM QRadar。
* [透過 syslog 傳送到 QRadar 的活動和 ASC 警示 (英文)](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – 此部落格文章提供透過 syslog 將活動和 Azure 資訊安全中心警示傳送至 QRadar 的步驟
* [Azure 記錄整合常見問題集 (FAQ)](security-azure-log-integration-faq.md) - 此常見問題集會回答有關 Azure 記錄整合的問題。
* [以 Azure 記錄整合來整合資訊安全中心警示](../security-center/security-center-integrating-alerts-with-log-integration.md) - 這份文件說明如何將資訊安全中心警示以及 Azure 診斷和 Azure 稽核記錄檔所收集的虛擬機器安全性事件，與您的 Log Analytics 或 SIEM 方案進行同步處理。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

