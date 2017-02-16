---
title: "在 Azure Logic Apps 中使用 SAP 連接器搭配內部部署資料閘道 | Microsoft Docs"
description: "Logic Apps 可讓您在工作流程中輕鬆地連接到內部部署 SAP 系統。"
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>開始使用 SAP 連接器 

混合式雲端連線是 Logic Apps 的核心。 內部部署資料閘道可讓您管理資料，並且從 Logic Apps 安全地存取內部部署的資源。 在本文中，我們將利用一個簡單案例來示範如何連接到內部部署 SAP 系統：透過 HTTP 要求 IDOC，並傳回回應。    

 > [!NOTE]
 > 此 SAP 連接器支援下列 SAP 系統 Logic Apps 中目前有區塊要求超過 90 秒的逾時限制 對於檔案選擇器中可顯示的欄位數目前仍有限制 (可能要以手動方式加入路徑)
 >
 >

## <a name="prerequisites"></a>必要條件
- 安裝及設定最新的[內部部署資料閘道](https://www.microsoft.com/en-us/download/details.aspx?id=53127)。  

    如果您尚未安裝內部部署資料閘道，請安裝最新的 1.15.6150.1 版或更新版本。 [這篇文章](http://aka.ms/logicapps-gateway)中可找到相關指示。 必須先在內部部署機器上安裝閘道，才能繼續執行其餘的步驟。

- 在您已安裝通用閘道的同一部電腦上，下載並安裝最新的 SAP 用戶端程式庫

## <a name="use-sap-connector"></a>使用 SAP 連接器

1. 讓我們建立 HTTP 要求觸發程序，然後在搜尋欄位中輸入「SAP」來選取 SAP 連接器動作。    
 ![搜尋 SAP](media/logic-apps-using-sap-connector/picture1.png)

2. 選擇「SAP」(根據 SAP 設定，為 ApplicationHost 或 MessagingHost)，並使用通用閘道為它建立連線。
 - 如果您目前沒有連線，系統會提示您建立一個。
 - 核取 [透過內部部署資料閘道連接] 選項，您會看到其他欄位在選取此核取方塊後立即顯示。
 - 指定連接名稱字串
 - 選取您在上一個步驟中安裝的閘道，或選取 [安裝閘道器] 來安裝新的閘道。   
 ![將連接字串新增到 SAP](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > 有兩種不同 SAP 連線，一個用於[應用程式主機](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)，另一個用於[傳訊主機](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)
  >
  >

3. 提供所有詳細資料後，請按一下 [建立]。 Logic Apps 會設定並測試連線，以確定它運作正常。 簽出所有項目之後，您就會看見適用於先前所選取之卡片的選項，請使用檔案選擇器來尋找正確的 IDOC 類別，或者手動輸入路徑，然後在內文欄位中選取 HTTP 回應。    
 ![SAP 動作](media/logic-apps-using-sap-connector/picture3.png)

4. 藉由新增新的動作來建立 HTTP 回應，回應訊息應該都來自 SAP 輸出

5. 儲存您的邏輯應用程式，並透過 HTTP 觸發程序 URL 傳送 IDOC 加以測試

6. 一旦傳送 IDOC 之後，即會等待來自邏輯應用程式的回應   

  > [!TIP]
  > 請查看如何[監視 Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)。
  >
  >

7. 全部完成，您現在擁有使用 SAP 連接器的運作中邏輯應用程式。 您可以開始探索它所提供的其他功能︰
  - BAPI
  - RFC

## <a name="next-steps"></a>後續步驟
- 了解[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。 
- 建立 Logic Apps 的[內部部署連線](../logic-apps/logic-apps-gateway-connection.md)。


<!--HONumber=Jan17_HO3-->


