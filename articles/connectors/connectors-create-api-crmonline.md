---
title: "將 Dynamics 365 (線上) 連接器新增到 Azure Logic Apps | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 Dynamics 365 (線上) 連接提供者提供 API，以便使用 Dynamics 365 (線上) 上的實體。"
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a
ms.lasthandoff: 02/14/2017


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>使用 Dynamics 365 連接器建立邏輯應用程式

使用 Logic Apps，您可以連線到 Dynamics 365 (線上)，並建立實用的商務流程以建立新的記錄、更新項目或傳回記錄清單。 使用 Dynamics 365 連接器，您可以︰

* 根據您從 Dynamics 365 (線上) 所取得的資料，來建置您的商務流程。
* 使用可取得回應的動作，然後提供輸出讓其他動作使用。 舉例來說，當 Dynamics 365 (線上) 中有項目更新時，您可以利用 Office 365 來傳送電子郵件。

本主題說明如何建立邏輯應用程式，以在每次 Dynamics 365 中建立了新的潛在客戶時，便在 Dynamics 365 建立工作。

## <a name="prerequisites"></a>必要條件
* 一個 Azure 帳戶。
* Dynamics 365 (線上) 帳戶。

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>逐步解說︰在每次 Dynamics 365 中建立了新的潛在客戶時便建立工作
1.    [登入 Azure](https://portal.azure.com)。
2.    在 [搜尋] 方塊中輸入 [Logic Apps]，然後按 ENTER 鍵。
3.    在 [Logic Apps 服務] 區域中，按一下 [新增]。

  ![LogicApp 新增](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    完成 [名稱]、[訂用帳戶]、[資源群組] 和 [位置] 欄位以建立邏輯應用程式物件，然後按一下 [建立]。

5.    選取新的邏輯應用程式。 當您收到 [部署成功] 通知時，按一下 [重新整理]。

6.    在 [開發工具] 下按一下 [邏輯應用程式設計工具]，然後在可用範本清單中按一下 [空白 LogicApp]。

7.    輸入「Dynamics 365」。 在有數個 Dynamics 365 觸發程序的清單內，按一下 [Dynamics 365 - 建立記錄時]。

8.    如果系統提示您登入 Dynamics 365，請立即登入。

9.    在觸發程序詳細資料中，輸入下列資訊。

  * **組織名稱**。 選取要讓邏輯應用程式接聽的 Dynamics 365 執行個體。

  * **實體名稱**。 選取您要接聽的實體，使其做為用來起始邏輯應用程式的觸發程序。 在本逐步解說中，我們選取 [潛在客戶]。

  * **多久要檢查一次項目？** 這些值會設定邏輯應用程式要多久檢查一次是否有觸發程序的相關更新。 預設設定是每三分鐘檢查是否有更新。

    * **頻率**。 選取秒、分鐘、小時或天。

    * **間隔**。 輸入數字來表示在下一次檢查之前要經過的秒數、分鐘數、小時數、或天數。

    ![邏輯應用程式觸發程序的詳細資料](./media/connectors-create-api-crmonline/trigger-details.png)

10.    按一下 [新增步驟]，然後按一下 [新增動作]。

11.    輸入「Dynamics 365」，然後在清單中按一下 [Dynamics 365 - 建立新記錄]。

12.    輸入以下資訊：
  * **組織名稱**。 選取要讓流程在其中建立記錄的 Dynamics 365 執行個體。 請注意，它不一定要是觸發事件的相同執行個體。
  * **實體名稱**。 選取要在觸發事件時建立記錄的實體。 在本逐步解說中，我們選取 [工作]。

13.    [主旨] 方塊隨即出現。 當您按一下該方塊時，便會顯示動態內容窗格，供您選取下列欄位。
  * **姓氏**。 選取此欄位，便會在建立工作記錄時於工作的 [主旨] 欄位中插入潛在客戶的姓氏。
  * **主題**。 選取此欄位，便會在建立工作記錄時於工作的 [主旨] 欄位中插入潛在客戶的 [主題] 欄位。
按一下 [主題] 將其新增至 [主旨] 方塊。

  ![邏輯應用程式建立新記錄詳細資料](./media/connectors-create-api-crmonline/create-record-details.png)

14.    按一下邏輯應用程式設計工具之工具列上的 [儲存]。

  ![邏輯應用程式設計工具工具列儲存](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    若要啟動邏輯應用程式，請按一下 [執行]。

  ![邏輯應用程式設計工具工具列儲存](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. 現在，在 Dynamics 365 中建立銷售潛在客戶記錄，然後看看流程的運作！

## <a name="using-advanced-options"></a>使用進階選項
當您在邏輯應用程式中新增步驟時，按一下 [顯示進階選項] 可讓您控制如何新增篩選或排序依據查詢，以在步驟中篩選資料。

例如，您可以使用篩選查詢來僅擷取作用中帳戶並依帳戶名稱排序。 若要這樣做，請輸入 OData 篩選查詢 **statuscode eq 1**，然後從動態內容窗格中選取 [帳戶名稱]。 詳細資訊︰[MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) 和 [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2)。

  ![LogicApp 進階選項](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>使用進階選項時的最佳作法
請注意，當您在欄位中新增值時，無論是輸入值或從顯示的動態內容中選取值時，您都必須符合欄位類型。

欄位類型  |使用方式  |所在位置  |名稱  |資料類型  
---------|---------|---------|---------|---------
文字欄位|文字欄位需要單行文字或屬於文字類型欄位的動態內容。 範例包括 [類別] 和 [子類別] 欄位。|[設定] > [自訂] > [自訂系統] > [實體] > [工作] > [欄位] |category |單行文字。       
整數欄位 | 某些欄位需要整數或屬於整數類型欄位的動態內容。 範例包括 [完成百分比] 和 [持續時間]。 |[設定] > [自訂] > [自訂系統] > [實體] > [工作] > [欄位] |percentcomplete |整數         
日期欄位 | 某些欄位需要以 mm/dd/yyyy 格式輸入的日期或屬於日期類型欄位的動態內容。 範例包括 [建立日期]、[開始日期]、[實際開始時間]、[上次保留時間]、[實際結束時間] 和 [到期日]。 | [設定] > [自訂] > [自訂系統] > [實體] > [工作] > [欄位] |createdon |日期和時間         
同時需要記錄識別碼和查閱類型的欄位 |某些參考另一個實體記錄的欄位同時需要記錄識別碼和查閱類型。 |[設定] > [自訂] > [自訂系統] > [實體] > [帳戶] > [欄位]  | accountid   | 主索引鍵

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>同時需要記錄識別碼和查閱類型的其他欄位範例
延續上表，這邊有更多無法與選自動態內容清單之值搭配運作的欄位範例。 相反地，這些欄位需要同時在 PowerApps 的欄位中輸入記錄識別碼和查閱類型。  
*  擁有者和擁有者類型。 [擁有者] 欄位必須是有效的使用者或小組記錄識別碼。 [擁有者類型] 必須是 [系統使用者] 或 [小組]。
* 客戶和客戶類型。 [客戶] 欄位必須是有效的帳戶或連絡人記錄識別碼。 [擁有者類型] 必須是 [帳戶] 或 [連絡人]。
* 關於和關於類型。 [關於] 欄位必須是有效的記錄識別碼，例如帳戶或連絡人記錄識別碼。 [關於類型] 必須是記錄的查閱類型，例如 [帳戶] 或 [連絡人]。

下列工作建立動作範例會新增帳戶記錄，其對應至會將其新增至工作之 [關於] 欄位的記錄識別碼。

  ![流量記錄識別碼和類型帳戶](./media/connectors-create-api-crmonline/recordid-type-account.png)

此範例也會根據使用者的記錄識別碼，將工作指派給特定使用者。
  ![流量記錄識別碼和類型帳戶](./media/connectors-create-api-crmonline/recordid-type-user.png)

若要尋找記錄的識別碼，請參閱下面的＜尋找記錄識別碼＞章節。

## <a name="find-the-record-id"></a>尋找記錄識別碼
1. 開啟記錄，例如帳戶記錄。

2. 在 [動作] 工具列上按一下 [彈出] ![彈出記錄](./media/connectors-create-api-crmonline/popout-record.png)。
或者，在 [動作] 工具列上按一下 [以電子郵件傳送連結]，將完整 URL 複製到預設電子郵件程式。

3. 記錄識別碼會顯示在 URL 的 %7b 和 %7d 編碼字元中間。

  ![流量記錄識別碼和類型帳戶](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>疑難排解
若要針對邏輯應用程式中的失敗步驟進行疑難排解，請檢視事件的狀態詳細資料。

1. 在 [Logic Apps] 區域中，按一下您的邏輯應用程式，然後按一下 [概觀]。 隨即會顯示 [摘要] 區域，其中會提供邏輯應用程式的執行狀態。 如果有失敗的執行，請按一下您想要檢視詳細資訊的失敗事件。

  ![LogicApp 疑難排解步驟 1](./media/connectors-create-api-crmonline/tshoot1.png)

2. 按一下失敗的步驟來加以展開。

  ![LogicApp 疑難排解步驟 2](./media/connectors-create-api-crmonline/tshoot2.png)

3. 隨即會顯示步驟的詳細資料，此資料可協助您針對失敗原因進行疑難排解。

    ![LogicApp 疑難排解步驟 2](./media/connectors-create-api-crmonline/tshoot3.png)

如需針對邏輯應用程式進行疑難排解的詳細資訊，請參閱[診斷邏輯應用程式失敗](../logic-apps/logic-apps-diagnosing-failures.md)。

## <a name="technical-details"></a>技術詳細資料
## <a name="triggers"></a>觸發程序
| 觸發程序 | 說明 |
| --- | --- |
| 當檔案建立時 |當 Dynamics 365 中有物件建立時，就會觸發某個流程。 |
| 當記錄更新時 |當 Dynamics 365 中有物件遭到修改時，就會觸發某個流程。 |
| 當記錄刪除時 |當 Dynamics 365 中有物件刪除時，就會觸發某個流程。 |

## <a name="actions"></a>動作
| 動作 | 說明 |
| --- | --- |
| 列出記錄 |這項作業會取得實體的記錄。 |
| 建立新的記錄 |這項作業會建立實體的新記錄。 |
| 取得記錄 |這項作業會取得實體的指定記錄。 |
| 刪除記錄 |這項作業會從實體集合中刪除記錄。 |
| 更新記錄 |這項作業會更新實體的現有記錄。 |

### <a name="trigger-and-action-details"></a>觸發程序和動作詳細資料
在本節中，請查看每個觸發程序和動作的特定詳細資料，包括任何必要或選擇性的輸入屬性，以及任何與連接器相關聯的對應輸出。

#### <a name="when-a-record-is-created"></a>當檔案建立時
當 Dynamics 365 中有物件建立時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>當記錄更新時
當 Dynamics 365 中有物件遭到修改時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>當記錄刪除時
當 Dynamics 365 中有物件刪除時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |


星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### <a name="list-records"></a>列出記錄
這項作業會取得實體的記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>建立新的記錄
這項作業會建立實體的新記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="get-record"></a>取得記錄
這項作業會取得實體的指定記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |項目識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

#### <a name="delete-a-record"></a>刪除記錄
這項作業會從實體集合中刪除記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |項目識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

#### <a name="update-a-record"></a>更新記錄
這項作業會更新實體的現有記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |Dynamics 365 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |記錄識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

##### <a name="output-details"></a>輸出詳細資料
無。

## <a name="http-responses"></a>HTTP 回應
動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤。 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

