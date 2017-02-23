---
title: "從 Log Analytics 取消 Azure 自動化帳戶連結 | Microsoft Docs"
description: "此文章提供如何將 Azure 自動化帳戶從 OMS 工作區取消連結的概觀。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 02/07/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7cd65cd34846122ff14f6d5df61e4f61a7c1ac4f
ms.openlocfilehash: 56b09c2cfc14813b5efcb364c580787fec1bf639

---

# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>如何將自動化帳戶從 Log Analytics 工作區取消連結

Azure 自動化與 Log Analytics 整合，不僅支援所有自動化帳戶 Runbook 作業的主動式監視，而且是在您匯入下列相依於 Log Analytics 的解決方案時所必須的：

* [更新管理](../operations-management-suite/oms-solution-update-management.md)
* [變更追蹤](../log-analytics/log-analytics-change-tracking.md)
* [於下班時間啟動/停止 VM](automation-solution-vm-management.md)
 
若決定不想再讓自動化帳戶與 Log Analytics 整合，您可以直接從 Azure 入口網站將您的帳戶取消連結。  繼續之前，您必須先移除稍早所述的解決方案，否則此程序將無法完成。  檢閱您已匯入之特定解決方案的主題，以了解移除解決方案所需的步驟。  

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

## <a name="unlink-workspace"></a>取消連結工作區

1. 從 Azure 入口網站，開啟您的自動化帳戶，然後在 [自動化帳戶] 刀鋒視窗的 [帳戶] 刀鋒視窗中選取 [取消連結工作區]。<br><br> ![取消連結工作區選項](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. 在 [取消連結工作區] 刀鋒視窗上，按一下 [取消連結工作區]。<br><br> ![取消連結工作區刀鋒視窗](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png)。<br><br>  您會收到提示，確認您想要繼續。<br><br>
3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知] 下追蹤進度。

若使用「更新管理」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 更新排程。  每個都會有符合您建立之更新部署的名稱)

* 針對解決方案建立的混合式背景工作角色群組。  每個都會具有如下名稱：machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)。

若使用「於下班時間啟動/停止 VM」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 啟動及停止 VM Runbook 排程 
* 啟動及停止 VM Runbook
* 變數   

## <a name="next-steps"></a>後續步驟

若要重新設定您的自動化帳戶以與 OMS Log Analytics 整合，請參閱[從自動化將作業狀態和作業串流轉送到 Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md)。 


<!--HONumber=Feb17_HO2-->


