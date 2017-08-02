---
title: "接收服務通知的活動記錄警示 | Microsoft Docs"
description: "在 Azure 服務發生時透過 SMS、電子郵件或 Webhook 獲得通知。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 202e7ec116e5e49beaad8c2d570a3659236e9b0f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="create-activity-log-alerts-on-service-notifications"></a>建立服務通知的活動記錄警示
## <a name="overview"></a>概觀
本文將說明如何使用 Azure 入口網站為服務健康狀態通知設定活動記錄警示。  

您可以在 Azure 傳送服務健康狀態通知到您的 Azure 訂用帳戶時接收警示。  
您可以針對下列設定警示：
- 服務健康狀態通知的類別 (事件、維護、資訊等)
- 受影響的服務
- 受影響的區域
- 通知的狀態 (使用中與已解決)
- 通知的層級 (資訊、警告、錯誤)

您也可以設定應傳送警示的對象：
- 選取現有的動作群組
- 建立新動作群組 (稍後可用於未來的警示)

您可以在[這裡](monitoring-action-groups.md)深入了解動作群組

如需使用 Azure Resource Manager 範本設定服務健康狀態通知警示的資訊：[Resource Manager 範本](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>使用 Azure 入口網站為新動作群組建立服務健康狀態通知的警示
1.  在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.  按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄檔]  區段。

3.  現在按一下 [警示] 一節。

    ![Alerts](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.  選取**新增活動記錄警示**命令，並填寫各欄位

    ![新增警示](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.  為活動記錄警示**命名**，並提供**描述**。

    ![新增警示新動作群組](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.  **訂用帳戶**是要儲存活動記錄警示的位置。 系統會在您目前用來作業的訂用帳戶中自動填入資訊。 這是將部署與監視警示資源的訂用帳戶。

7.  選擇**訂用帳戶**中會與此警示產生關聯的 [資源群組]。

8.  在 [事件類別目錄] 下，選取 [服務健全狀況] 選項。 選擇您想要接收通知之相關服務健康狀態通知的 [服務]、[區域]、[類型]、[狀態] 和 [層級]。

9.  建立**新增**動作群組，提供**名稱**和**簡短名稱**；將在發動這個警示時於傳送的通知中參考此簡短名稱。

10. 然後，透過提供接收者的下列各項來定義接收者的清單

    a. **名稱：**接收者名稱、別名或識別碼。

    b.這是另一個 C# 主控台應用程式。 **動作類型：**選擇透過 SMS、電子郵件或 Webhook 連絡接收者

    c. **詳細資料：**根據選擇的動作類型，提供電話號碼、電子郵件地址或 Webhook URI。

11. 完成後選取 [確定]  建立警示。

在幾分鐘之內，警示會開始作用，且先前所述觸發。

如需活動記錄警示 Webhook 結構描述的詳細資料，請[按一下這裡](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>在這些步驟中定義的動作群組，將以現有的動作群組的形式提供所有未來的警示定義重複使用。
>
>

## <a name="create-an-alert-on-a-service-health-notification-using-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 入口網站，使用現有動作群組建立服務健康狀態通知的警示
1.  在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.  按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄檔]  區段。

3.  現在按一下 [警示] 一節。

    ![Alerts](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.  選取**新增活動記錄警示**命令，並填寫各欄位

    ![新增警示](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.  為活動記錄警示**命名**，並選擇**描述**。

    ![新增警示現有動作群組](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.  **訂用帳戶**是要儲存活動記錄警示的位置。 系統會在您目前用來作業的訂用帳戶中自動填入資訊。 這是將部署與監視警示資源的訂用帳戶。

7.  選擇**訂用帳戶**中會與此警示產生關聯的 [資源群組]。

8.  在 [事件類別目錄] 下，選取 [服務健全狀況] 選項。 選擇您想要接收通知之相關服務健康狀態通知的 [服務]、[區域]、[類型]、[狀態] 和 [層級]。

9.  針對 [透過下列方式通知] 選擇 [現有的動作群組]。 選取適當的動作群組。

10. 完成後選取 [確定]  建立警示。

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="managing-your-alerts"></a>管理警示

一旦您已建立警示，它將會顯示在監視服務的 [警示] 區段中。 選取您想要管理的警示，您將能夠：
* **編輯**它。
* **刪除**它。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。

## <a name="next-steps"></a>後續步驟：
- 深入了解[服務健康狀態通知](monitoring-service-notifications.md)
- 深入了解[通知速率限制](monitoring-alerts-rate-limiting.md)
- 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
- 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示  
- 深入了解[動作群組](monitoring-action-groups.md)

