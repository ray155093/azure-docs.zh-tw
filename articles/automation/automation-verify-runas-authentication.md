---
title: "驗證 Azure 自動化帳戶組態 | Microsoft Docs"
description: "本文說明如何確認已正確設定您的自動化帳戶組態。"
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
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>測試 Azure 自動化執行身分帳戶驗證
成功建立自動化帳戶之後，您可以執行簡單的測試，確認您能夠在 Azure Resource Manager 或 Azure 傳統部署中使用新建立或已更新的自動化執行身分帳戶成功進行驗證。    

## <a name="automation-run-as-authentication"></a>自動化執行身分驗證

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。  
2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。
3. 選取 **AzureAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。  您會收到提示，確認您想要啟動 Runbook。
4. 已建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。  
5. 作業狀態一開始會顯示為 [已排入佇列]  ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。 然後當背景工作宣告該工作時，狀態將變更為 [正在開始]，然後 Runbook 實際開始執行時再變更為 [執行中]。  
6. Runbook 作業完成時，我們應該會看到 [完成] 狀態。<br> ![安全性主體 Runbook 測試](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. 若要查看 Runbook 的詳細結果，請按一下 [輸出]  圖格。
8. 在 [輸出]  刀鋒視窗中，您應該會看到它已成功驗證並傳回資源群組中的所有可用資源清單。
9. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。
10. 關閉 [作業摘要] 和對應的 **AzureAutomationTutorialScript** Runbook 刀鋒視窗。

## <a name="classic-run-as-authentication"></a>傳統執行身分驗證
如果您即將管理傳統部署模型中的資源，請執行下列步驟，以確認您能夠使用新的傳統執行身分帳戶成功進行驗證。     

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。  
2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。
3. 選取 **AzureClassicAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。  您會收到提示，確認您想要啟動 Runbook。
4. 已建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。  
5. 作業狀態一開始會顯示為 [已排入佇列]  ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。 然後當背景工作宣告該工作時，狀態將變更為 [正在開始]，然後 Runbook 實際開始執行時再變更為 [執行中]。  
6. Runbook 作業完成時，我們應該會看到 [完成] 狀態。<br><br> ![安全性主體 Runbook 測試](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. 若要查看 Runbook 的詳細結果，請按一下 [輸出]  圖格。
8. 在 [輸出]  刀鋒視窗中，您應該會看到它已成功驗證並傳回訂用帳戶中的所有傳統 VM 清單。
9. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。
10. 關閉 [作業摘要] 和對應的 **AzureClassicAutomationTutorialScript** Runbook 刀鋒視窗。

## <a name="next-steps"></a>後續步驟
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

