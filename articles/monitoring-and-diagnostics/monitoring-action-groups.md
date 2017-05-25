---
title: "在 Azure 入口網站中建立和管理動作群組 | Microsoft Docs"
description: 
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
ms.date: 05/15/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 240f7f3788f6a432bcb1ec3b244bc76e3157445a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
## <a name="overview"></a>概觀 ##
本文將說明如何在 Azure 入口網站中建立和管理動作群組。

動作群組可讓您設定接收者清單。 然後這些群組就可在定義活動記錄警示時加以運用；確保觸發活動記錄警示時，特定的動作群組可以收到通知。

動作群組的每個動作類型可以有多達 10 個。 動作的定義是由以下各項組合而成：

**名稱：**動作群組內的唯一識別碼。  
**動作類型：**這會定義要執行的動作。 選項為傳送 SMS、傳送電子郵件或呼叫 Webhook。  
**詳細資料：**根據動作類型，必須提供對應的電話號碼、電子郵件地址或 Webhook URI。

您可以透過以下管道設定並取得服務健全狀況通知警示的相關資訊：
* [Azure 入口網站](monitoring-action-groups.md)
- [Resource Manager 範本](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>建立 Azure 入口網站的動作群組 ##
1.    在[入口網站](https://portal.azure.com)中，瀏覽至 [監視] 服務

    ![監視](./media/monitoring-action-groups/home-monitor.png)
2.    按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 此刀鋒視窗會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔]  區段。

3.    現在按一下 [動作群組] 區段

    ![動作群組](./media/monitoring-action-groups/action-groups-blade.png)
4.    按一下 [新增] 動作群組命令，並填入欄位

    ![新增動作群組](./media/monitoring-action-groups/add-action-group.png)
5.    提供動作群組的**名稱**和**簡短名稱**；將在傳送至這個群組的通知中參考此簡短名稱

      ![動作群組定義](./media/monitoring-action-groups/action-group-define.png)

6.    **訂用帳戶**是要儲存動作群組的位置。 系統會在您目前用來作業的訂用帳戶中自動填入資訊。

7.    選擇此動作群組的 [資源群組]。

8.    然後透過以下的組合定義動作清單：
  1. **名稱：**動作群組內的唯一識別碼。
  2. **動作類型：**這會定義要執行的動作。 選項為傳送 SMS、傳送電子郵件或呼叫 Webhook。
  3. **詳細資料：**根據動作類型，必須提供對應的電話號碼、電子郵件地址或 Webhook URI。

9.    完成後選取 [確定] 來建立動作群組。

## <a name="managing-your-action-groups"></a>管理您的動作群組 ##
一旦您已建立動作群組，將會在監視服務的 [動作群組] 區段中顯示。 選取您想要管理的動作群組，您將能夠進行下列動作：
* 新增、編輯或移除接收者。
-    刪除動作群組。

## <a name="next-steps"></a>後續步驟： ##
進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)  
了解[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)  
深入了解警示的[速率限制](monitoring-alerts-rate-limiting.md)  
取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示  
如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)

