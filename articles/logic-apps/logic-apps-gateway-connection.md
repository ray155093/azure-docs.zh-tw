---
title: "存取內部部署資料 - Azure Logic Apps | Microsoft Docs"
description: "邏輯應用程式如何藉由連線至內部部署資料閘道來存取內部部署資料。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ef7df25d8080cae41235dffb287906508d4a652d
ms.lasthandoff: 03/30/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>從邏輯應用程式連線至內部部署資料

若要存取內部部署資料，您可以為受支援的 Azure Logic Apps 連接器設定內部部署資料閘道的連線。 下列步驟可引導您了解如何安裝和設定內部部署資料閘道，以搭配邏輯應用程式使用。
內部部署資料閘道支援這些資料來源連線︰

*   BizTalk Server
*    DB2  
*   檔案系統
*   Informix
*   MQ
*    Oracle 資料庫 
*   SAP 應用程式伺服器 
*   SAP 訊息伺服器
*    SQL Server

如需這些連線的詳細資訊，請參閱[Azure Logic Apps 的連接器](https://docs.microsoft.com/azure/connectors/apis-list)。

## <a name="requirements"></a>需求

* 您必須在 Azure 中擁有工作或學校電子郵件地址，以便將內部部署資料閘道與您的帳戶 (Azure Active Directory 帳戶) 相關聯。

* 如果您使用 Microsoft 帳戶 (例如 @outlook.com)，您可以使用 Azure 帳戶[建立工作或學校電子郵件地址](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)。

* 您必須[已在本機電腦上安裝內部部署資料閘道](logic-apps-gateway-install.md)。

* 您只能將安裝關聯至一個閘道資源。 您的閘道無法供另一個 Azure 內部部署資料閘道進行宣告。 宣告會發生在 ([本主題步驟 2 期間的建立作業](#2-create-an-azure-on-premises-data-gateway-resource))。

## <a name="install-and-configure-the-connection"></a>安裝和設定連線

### <a name="1-install-the-on-premises-data-gateway"></a>1.安裝內部部署資料閘道

如果您還沒有這麼做，請依照下列步驟來[安裝內部部署資料閘道](logic-apps-gateway-install.md)。 在繼續進行其他步驟前，請先確定您已在內部部署機器上安裝資料閘道。

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2.建立 Azure 內部部署資料閘道資源

安裝閘道之後，您必須將 Azure 訂用帳戶與閘道相關聯。

> [!IMPORTANT] 
> 請確定閘道資源建立於與您邏輯應用程式相同的 Azure 區域中。 如果您未將它部署到相同的區域，將無法在您的邏輯應用程式中存取該資源。 
> 

1. 使用安裝閘道期間所使用的同一個工作或學校電子郵件地址來登入 Azure。
2. 選擇 [新增]。
3. 尋找並選取 [內部部署資料閘道]。
4. 若要將閘道與您的帳戶相關聯，請填妥資訊，包括選取適當的 [安裝名稱]。
   
    ![內部部署資料閘道連接][1]

5. 若要建立資源，請選擇 [建立]。

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3.在邏輯應用程式設計工具中建立邏輯應用程式連線

現在，您的 Azure 訂用帳戶已經與內部部署資料閘道的執行個體相關聯，您可以從邏輯應用程式建立與閘道的連線。

1. 開啟邏輯應用程式，然後選擇支援內部部署連線能力的連接器 (例如 SQL Server)。
2. 選取 [透過內部部署資料閘道連線]。
   
    ![邏輯應用程式設計工具閘道建立][2]

3. 選取要連線的 [閘道]，並完成所需的任何其他連線資訊。
4. 若要建立連線，請選擇 [建立]。

連線現已設定好，可供邏輯應用程式使用。

## <a name="data-gateway-connection-modifications"></a>修改資料閘道連線
一旦您將資料閘道連線新增到邏輯應用程式之後，您可能需要進行修改以調整該連線特定的設定。 您可以在下列其中一個位置找到連線：
* 在邏輯應用程式的主要刀鋒視窗上，您應該會在 [開發工具] 區段中看見 API 連線的面板。 選取將顯示所有與邏輯應用程式相關聯的 API 連線的選項，其中一個便是您的資料閘道連線。 選取該選項，您接著就能檢視和修改與連線相關聯的設定。
* 選取 [API 連線] 主要刀鋒視窗，將顯示訂用帳戶中的所有 API 連線。 您的資料閘道連線將在此清單中。 選取該選項，您就能檢視和修改與連線相關聯的設定。

## <a name="next-steps"></a>後續步驟

* [Logic Apps 範例和常見案例](../logic-apps/logic-apps-examples-and-scenarios.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

