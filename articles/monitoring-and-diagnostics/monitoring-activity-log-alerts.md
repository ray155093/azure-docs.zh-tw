---
title: "建立活動記錄警示 | Microsoft Docs"
description: "活動記錄中發生特定事件時，透過 SMS、Webhook 及電子郵件收到通知。"
author: anirudhcavale
manager: carmonm
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
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>建立活動記錄警示

## <a name="overview"></a>概觀
「活動記錄警示」是 Azure 資源，因此可使用 Azure 入口網站或 Azure Resource Manager (ARM) 加以管理。 本文將說明如何使用 Azure 入口網站來設定活動記錄事件的警示。

您可以接收有關您訂用帳戶中資源執行的作業，或可能會影響您資源健康狀態的服務健康狀態事件的警示。 活動記錄警示會監視活動記錄事件，以取得部署警示的特定訂用帳戶。

您可以針對下列設定警示：
* 事件類別目錄 (針對[服務健全狀況事件，請按一下這裡](monitoring-activity-log-alerts-on-service-notifications.md))
- 資源群組
- 資源
- 資源類型
- 作業名稱
- 通知的層級 (詳細資訊、資訊、警告、錯誤、重大)
- 狀態
- 事件起始者

您也可以設定應傳送警示的對象：
* 選取現有的動作群組
- 建立新動作群組 (稍後可用於未來的警示)

您可以在[這裡](monitoring-action-groups.md)深入了解動作群組

您可以透過以下管道設定並取得服務健康狀態通知警示的相關資訊
* [Azure 入口網站](monitoring-activity-log-alerts.md)
- [Resource Manager 範本](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>使用 Azure 入口網站為新動作群組建立活動記錄事件的警示
1.    在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄檔]  區段。

3.    現在按一下 [警示] 一節。

    ![Alerts](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    選取**新增活動記錄警示**命令，並填寫各欄位

5.    為活動記錄警示**命名**，並選擇**描述**。 當此警示發動時，這些會出現在傳送的通知中。

    ![新增警示](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    系統應該會將 [訂用帳戶] 自動填入至您目前用來作業的訂用帳戶下。 這是將部署與監視警示資源的訂用帳戶。

    ![新增警示新動作群組](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    選擇**訂用帳戶**中會與此警示產生關聯的 [資源群組]。

8.    提供**事件類別目錄**、**資源群組**、**資源**、**資源類型**、**作業名稱**、**層級**、**狀態**和**事件起始者**值來識別此警示應監視的事件。

9.    建立**新增**動作群組，提供**名稱**和**簡短名稱**；系統會在啟動這個警示時，在傳送的通知中參考此簡短名稱。

10.    然後，透過提供接收者的下列各項來定義接收者的清單

    a. **名稱：**接收者名稱、別名或識別碼。

    b.這是另一個 C# 主控台應用程式。 **動作類型：**選擇透過 SMS、電子郵件或 Webhook 連絡接收者

    c. **詳細資料：**根據選擇的動作類型，提供電話號碼、電子郵件地址或 Webhook URI。

11.    完成後選取 [確定]  建立警示。

在幾分鐘之內，警示會開始作用，且先前所述觸發。

如需活動記錄警示 Webhook 結構描述的詳細資料，請[按一下這裡](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>在這些步驟中定義的動作群組，將以現有的動作群組的形式提供所有未來的警示定義重複使用。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 入口網站為現有動作群組建立活動記錄事件的警示
1.    在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄檔]  區段。

3.    現在按一下 [警示] 一節。

    ![Alerts](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    選取**新增活動記錄警示**命令，並填寫各欄位

5.    為活動記錄警示**命名**，並選擇**描述**。 當此警示發動時，這些會出現在傳送的通知中。

    ![新增警示](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    系統應該會將 [訂用帳戶] 自動填入至您目前用來作業的訂用帳戶下。

    ![新增警示現有動作群組](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    選擇此警示的 [資源群組]。

8.    提供**事件類別目錄**、**資源群組**、**資源**、**資源類型**、**作業名稱**、**層級**、**狀態**和**事件起始者**值來設定此警示應套用的事件範圍。

9.    針對 [透過下列方式通知] 選擇 [現有的動作群組]。 選取個別的動作群組。

10.    完成後選取 [確定]  建立警示。

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="managing-your-alerts"></a>管理警示

一旦您已建立警示，它將會顯示在監視服務的 [警示] 區段中。 選取您想要管理的警示，您將能夠：
* **編輯**它。
* **刪除**它。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。

## <a name="next-steps"></a>後續步驟：
取得[警示概觀](monitoring-overview-alerts.md)  
檢閱[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)深入了解[動作群組](monitoring-action-groups.md)  
深入了解[服務健康狀態通知](monitoring-service-notifications.md)

