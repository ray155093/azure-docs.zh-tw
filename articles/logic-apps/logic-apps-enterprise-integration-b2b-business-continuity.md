---
title: "邏輯應用程式 B2B 整合帳戶災害復原 - Azure 邏輯應用程式 | Microsoft Docs"
description: "Azure 邏輯應用程式 B2B 災害復原"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>邏輯應用程式 B2B 跨地區災害復原
B2B 工作負載涉及金錢交易，例如訂單、發票。  對於商務，務必快速復原，以便符合與合作夥伴對於災難事件達成的商務層級 SLA。  本主題示範如何建置 B2B 工作負載的商務持續性計劃。  涵蓋內容

* 在次要區域中建立整合帳戶
* 建立從主要區域到次要區域的連接 
* 在災難事件期間容錯移轉到次要區域
* 發生災難事件後切換回主要區域

## <a name="create-an-integration-account-in-secondary-region"></a>在次要區域中建立整合帳戶
1. 選取次要區域，並建立[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。  

2. 對於執行狀態需要複寫到次要區域整合帳戶的必要訊息流程，新增夥伴、結構描述和合約。

    > [!Tip]
    > 請確定區域之間的整合帳戶成品命名慣例保持一致 
    > 
    > 

3. 建議也在次要區域中部署所有的主要區域資源 (例如，SQL Azure 或 DocumentDB 資料庫，或傳訊、APIM、邏輯應用程式所用的服務匯流排 / 事件中樞) 次要區域中。  

## <a name="establish-a-connection-from-primary-to-secondary"></a>建立從主要到次要的連接 
若要提取主要區域的執行狀態，請在次要區域中建立邏輯應用程式。  它應該有**觸發程序**和**動作**。  觸發程序應該連接至主要區域整合帳戶，而且動作應該連接至次要區域整合帳戶。  按照時間間隔，觸發程序會輪詢主要區域執行狀態資料表提取新的記錄 (如果有)，而且動作會將這些新的記錄更新至次要區域整合帳戶。 這有助於從主要區域將累加式執行階段狀態更新到次要區域。

邏輯應用程式整合帳戶中的商務持續性是設計來按照 B2B 通訊協定 (X12、AS2 和 EDIFACT) 進行支援。  若要了解詳細的步驟，請選取個別的連結。

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (敬請期待)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>在災難事件期間容錯移轉到次要區域
在災難事件期間，沒有商務持續性的主要區域可用時，會將流量導向次要區域。 次要區域有助於迅速恢復商務功能，以符合與交易夥伴達成的復原時間/點目標 (RPO/RTO)。  此外，從一個區域容錯移轉至另一個區域僅需要最低程度的作業過程。 

從主要區域將控制編號複製到次要區域會有預期的延遲。  若要避免在災害事件期間將重複產生的控制編號傳送給合作夥伴，建議使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 在**次要區域合約**中增加控制編號。

## <a name="fall-back-to-primary-region-post-disaster-event"></a>發生災難事件後切換回主要區域
主要區域可供使用時，請遵循以下步驟切換回主要區域     
* 停止接受**次要區域**中的合作夥伴發出的訊息   
* 使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 對於我有的**主要區域合約**增加產生的控制編號   
* 從次要區域將流量導向到主要區域   
* 檢查已啟用在次要區域中建立邏輯應用程式以從主要區域提取執行狀態    

## <a name="x12"></a>X12 
EDI X12 文件的商務持續性是按照控制編號加以設計   
* 收到合作夥伴發出的控制編號 (輸入訊息)  
* 產生控制編號 (輸出訊息) 並傳送給合作夥伴  
    
    > [!Tip]
    > 您也可以使用 [X12 快速啟動範本](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)建立邏輯應用程式。  建立主要和次要整合帳戶是使用範本的必要條件。  這個範本可讓您建立 2 個邏輯應用程式，一個用於接收的控制編號，另一個用於產生的控制編號。  個別的觸發程序和動作是在邏輯應用程式中建立的，會將觸發程序連接至主要整合帳戶，並將動作連接至次要整合帳戶。
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>收到合作夥伴發出的控制編號
1. 在次要區域中建立[邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)   

2. 搜尋 **X12**，並選取 [X12 - 修改接收的控制編號時]    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. 選取觸發程序會提示連接至整合帳戶。 將連接至主要區域整合帳戶的觸發程序。  指定連接名稱，並選取清單中的 [主要區域整合帳戶]，然後按一下 [建立]。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. 開始控制編號同步的日期時間是選用的。  頻率可以設為**天**、**小時**、**分鐘**或**秒**的間隔。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. 按一下 [新增步驟] 和 [新增動作]    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. 搜尋 **X12**，並選取 [X12 - 新增或更新接收的控制編號時]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. 將連接至次要整合帳戶的動作。  選取 [變更連接]和 [新增連接] 列出可用的整合帳戶。  指定連接名稱，並選取清單中的 [次要區域整合帳戶]，然後按一下 [建立]。     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. 選取動態內容，並儲存邏輯應用程式   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. 按照時間間隔，觸發程序會輪詢主要區域接收的控制編號資料表，並提取新的記錄，而且動作會將這些新的記錄更新至次要區域整合帳戶。  如果沒有更新，則觸發程序狀態將顯示為忽略。
    
    > [!Tip]
    > 啟用合約接收設定的重複檢查會保留接收的控制編號執行階段狀態，並且有助於按照固定間隔進行觸發程序
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>產生控制編號並傳送給合作夥伴
1. 在次要區域中建立[邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)  

2. 搜尋 **X12**，並選取 [X12 - 修改產生的控制編號時]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. 選取觸發程序會提示連接至整合帳戶。 將連接至主要區域整合帳戶的觸發程序。  指定連接名稱，並選取清單中的 [主要區域整合帳戶]，然後按一下 [建立]。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. 開始控制編號同步的日期時間是選用的。  頻率可以設為**天**、**小時**、**分鐘**或**秒**的間隔。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. 按一下 [新增步驟] 和 [新增動作]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. 搜尋 **X12**，並選取 [X12 - 新增或更新產生的控制編號時]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. 將連接至次要整合帳戶的動作。  選取 [變更連接]和 [新增連接] 列出可用的整合帳戶。  指定連接名稱，並選取清單中的 [次要區域整合帳戶]，然後按一下 [建立]。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. 選取動態內容，並儲存邏輯應用程式   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. 按照時間間隔，觸發程序會輪詢主要區域接收的控制編號資料表，並提取新的記錄，而且動作會將這些新的記錄更新至次要區域整合帳戶。  如果沒有更新，則觸發程序狀態將顯示為忽略。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

按照時間間隔，累加式執行階段狀態會從主要區域複寫至次要區域。  在災難事件期間，沒有主要區域可用時，會對於商務持續性將流量導向次要區域。 

## <a name="as2"></a>AS2 
使用 AS2 通訊協定的文件本身的商務持續性是根據訊息識別碼和 MIC 值所設計

> [!Tip]
    > 您也可以使用 [AS2 快速啟動範本](https://github.com/Azure/azure-quickstart-templates/pull/3302)建立邏輯應用程式。  建立主要和次要整合帳戶是使用範本的必要條件。  該範本可協助建立有觸發程序和動作的邏輯應用程式。  建立從觸發程序到主要整合帳戶的連接，以及次要整合帳戶的動作。
    > 
    >

1. 在次要區域中建立[邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)  

2. 搜尋 **AS2**，並選取 [AS2 - 建立 MIC 值時]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. 選取觸發程序會提示連接至整合帳戶。 將連接至主要區域整合帳戶的觸發程序。  指定連接名稱，並選取清單中的 [主要區域整合帳戶]，然後按一下 [建立]。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. 啟動 MIC 值同步處理的日期時間是選用的。  頻率可以設為**天**、**小時**、**分鐘**或**秒**的間隔。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. 按一下 [新增步驟] 和 [新增動作]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. 搜尋 **AS2**，並選取 [AS2 - 新增或更新 MIC]   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. 將連接至次要整合帳戶的動作。  選取 [變更連接]和 [新增連接] 列出可用的整合帳戶。  指定連接名稱，並選取清單中的 [次要區域整合帳戶]，然後按一下 [建立]。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. 選取動態內容，並儲存邏輯應用程式   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. 按照時間間隔，觸發程序會輪詢主要區域資料表，並提取新的記錄，而且動作會將這些新的記錄更新至次要區域整合帳戶。  如果沒有更新，則觸發程序狀態將顯示為忽略。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

按照時間間隔，累加式執行階段狀態會從主要區域複寫至次要區域。  在災難事件期間，沒有主要區域可用時，會對於商務持續性將流量導向次要區域。 

## <a name="next-steps"></a>後續步驟
* [深入了解監視 B2B 訊息](logic-apps-monitor-b2b-message.md)。   
