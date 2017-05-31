---
title: "OMS 中的 IT 服務管理連接器 | Microsoft Docs"
description: "使用 IT 服務管理連接器，在 OMS 中將 ITSM 工作項目集中監視及管理，並快速解決所有問題。"
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0aa41bbc0e0135737d352553607f48a39757bcc3
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>使用 IT 服務管理連接器 (預覽) 將 ITSM 工作項目集中管理

您可以使用 OMS Log Analytics 中的 IT 服務管理連接器 (ITSMC)，將 ITSM 產品/服務之間的工作項目集中監視及管理。

IT 服務管理連接器會將您現有的 IT 服務管理 (ITSM) 產品和服務與 OMS Log Analytics 進行整合。  解決方案與 ITSM 產品/服務具有雙向整合，在其中讓 OMS 使用者可選擇在 ITSM 解決方案中建立事件、警示或活動。 連接器也會從 ITSM 解決方案將諸如事件和變更要求等資料匯入 OMS Log AnalyticsOMS。

利用 IT 服務管理連接器，您可以：

  - 將您組織內使用的 ITSM 產品/服務之工作項目集中監視及管理。
  - 從 OMS 警示及透過記錄搜尋，在 ITSM 中建立 ITSM 工作項目 (例如警示、事件、活動)。
  - 讀取 ITSM 解決方案的事件和變更要求，並與 Log Analytics 工作區中的相關記錄資料相互關聯。
  - 在使用者呼叫及將問題回報給支援人員之前，就找出任何非預期且不尋常的事件並加以解決。
  - 將工作項目資料匯入 Log Analytics，並建立關鍵效能指標 (KPI) 報告。  您可以使用這些報告來識別、評估和處理數個重要的項目，例如惡意程式碼評估。
  - 請檢視策劃儀表板，以獲得關於事件、變更要求及受影響系統的更深入見解。
  - 與 Log Analytics 工作區中的其他管理解決方案建立相互關聯來加速疑難排解。


## <a name="prerequisites"></a>必要條件

若要將 ITSM 工作項目匯入 OMS Log Analytics，解決方案會要求 OMS 中的 IT 服務管理連接器與您從中匯入工作項目的 ITSM 產品/服務之間的連線。


## <a name="configuration"></a>組態

使用[從方案庫新增 Log Analytics 解決方案](log-analytics-add-solutions.md)中所述的程序，將 IT 服務管理連接器新增至您的 OMS 工作區。

如您在方案庫中所見的 [IT 服務管理連接器] 圖格︰

![連接器圖格](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

新增成功之後，您會在 [OMS] > [設定] > [連接的來源] 下看到 IT 服務管理連接器。

![ITSMC 已連線](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

## <a name="management-packs"></a>管理組件
此解決方案不需要任何管理組件。

## <a name="connected-sources"></a>連接的來源

IT 服務管理連接器支援下列 ITSM 產品/服務︰

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>使用解決方案

一旦您將 OMS IT 服務管理連接器與 ITSM 服務連線時，Log Analytics 服務就會開始從連線的 ITSM 產品/服務收集資料。

> [!NOTE]
> - IT 服務管理連接器解決方案所匯入的資料會出現在 Log Analytics 中，作為名為 **ServiceDesk_CL** 的事件。
- 事件會包含一個名為 **ServiceDeskWorkItemType_s** 的欄位。 根據 **ServiceDesk_CL** 事件中所包含的工作項目資料，可採用其值作為事件或變更要求。

下列資訊說明 IT 服務管理連接器所收集之資料的範例︰

> [!NOTE]
> 根據匯入 Log Analytics 的工作項目類型，**ServiceDesk_CL** 會包含下列欄位︰

**工作項目︰****事件**  
ServiceDeskWorkItemType_s="Incident"

**欄位**

- ServiceDeskConnectionName
- 服務台識別碼
- State
- 急迫性
- 影響
- 優先順序
- 升級
- 建立者
- 解決者
- 關閉者
- 來源
- 指派對象
- 類別
- 課程名稱
- 說明
- 建立日期
- 關閉日期
- 解決日期
- 上次修改日期
- 電腦


**工作項目︰****變更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**欄位**
- ServiceDeskConnectionName
- 服務台識別碼
- 建立者
- 關閉者
- 來源
- 指派對象
- Title
- 類型
- 類別
- State
- 升級
- 衝突狀態
- 急迫性
- 優先順序
- 風險
- 影響
- 指派對象
- 建立日期
- 關閉日期
- 上次修改日期
- 要求日期
- 計劃開始日期
- 計劃結束日期
- 工作開始日期
- 工作結束日期
- 說明
- 電腦

ITSM 資料的範例 Log Analytics 畫面︰

![Log Analytics 畫面](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT 服務管理連接器 – 與其他 OMS 解決方案整合

IT 服務管理連接器，目前支援與服務對應解決方案整合。

服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 如果您想到您的伺服器，ADM 可讓您以互連系統 (提供重要服務) 的形式檢視它們。 不需要進行任何設定，只要安裝了代理程式，服務對應就會顯示橫跨任何 TCP 連接架構的伺服器、處理程序和連接埠之間的連接。 詳細資訊︰[服務對應](../operations-management-suite/operations-management-suite-service-map.md)。

透過這項整合，您可以檢視 ITSM 解決方案中建立的服務台項目，如下列範例中所示︰

![整合式解決方案 ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>建立 OMS 警示的 ITSM 工作項目

針對 OMS 警示，您可以在連線的 ITSM 來源中建立相關聯的工作項目。  若要這樣做，請使用下列程序：

1. 從 [記錄搜尋] 視窗中，執行記錄搜尋查詢來檢視資料。 查詢結果為工作項目的來源。
2. 在 [記錄搜尋] 中，按一下 [警示] 可開啟 [新增警示規則] 頁面。

    ![Log Analytics 畫面](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. 在 [新增警示規則] 視窗中，提供 [名稱]、[嚴重性]、[搜尋查詢] 和 [警示準則] (時間範圍/計量的度量單位) 的必要詳細資料。
4. 在 [ITSM 動作] 選取 [是]。
5. 從 [選取連線] 清單中選取您的 ITSM 連線。
6. 提供所需的詳細資料。
7. 若要為這項警示的每個記錄項目建立個別的工作項目，請選取 [為每個記錄項目建立個別的工作項目] 核取方塊。

    或

    將此核取方塊取消選取，可在此警示下對任意數目的記錄項目只建立一個工作項目。

7. 按一下 [儲存] 。

將會在 [警示] 下建立 OMS 警示。 符合所指定警示的條件時，會建立對應 ITSM 連線的工作項目。

## <a name="create-itsm-work-items-from-oms-logs"></a>從 OMS 記錄建立 ITSM 工作項目

您可以使用 OMS 記錄搜尋，在連線的 ITSM 來源中建立工作項目。 若要這樣做，請使用下列程序：

1. 從 [記錄搜尋] 中，搜尋必要的資料、選取詳細資料，然後按一下 [建立工作項目]。

    [建立 ITSM 工作項目] 視窗隨即出現︰

    ![Log Analytics 畫面](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   新增下列詳細資料︰

  - **工作項目標題**︰工作項目的標題。
  - **工作項目描述**︰新工作項目的描述。
  - **受影響電腦**︰找到此記錄資料的電腦之名稱。
  - **選取連線**：您要在其中建立此工作項目的 ITSM 連線。
  - **工作項目**︰工作項目的類型。

3. 若要使用事件的現有工作項目範本，請在 [根據範本產生工作項目] 選項下按一下 [是]，然後按一下 [建立]。

    或者，

    如果您想要提供自訂的值，請按一下 [否]。

4. 在 [連絡人類型]、[影響]、[急迫性]、[類別] 和 [子類別] 文字方塊中提供適當的值，然後按一下 [建立]。

會在 ITSM 中建立工作項目，您也可以在 OMS 中加以檢視。

## <a name="contact-us"></a>與我們連絡

如需關於 IT 服務管理連接器的任何查詢或意見反應，請與我們連絡：[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)。

## <a name="next-steps"></a>後續步驟
[將 ITSM 產品/服務新增至 IT 服務管理連接器](log-analytics-itsmc-connections.md)。

