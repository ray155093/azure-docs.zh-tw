---
title: "Azure 自動化之圖形化 Runbook 中的錯誤處理 | Microsoft Docs"
description: "本文說明如何在 Azure 自動化的圖形化 Runbook 中實作錯誤處理邏輯。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自動化之圖形化 Runbook 中的錯誤處理

您需要考慮的關鍵 Runbook 設計主體是找出 Runbook 可能會遇到的不同問題，例如成功、預期的錯誤狀態，以及非預期的錯誤狀況。  Runbook 應該據以包括要偵測的錯誤處理。  使用圖形化 Runbook 時，如果您想要驗證活動的輸出，或以適當方式處理錯誤，您可能要遵循 PowerShell 程式碼活動、定義活動輸出連結的條件式邏輯，或套用其他方法。          

當 Runbook 執行時，如果活動發生非終止錯誤，其後的任何活動往往無論如何仍會進行處理。  當然，它可能會產生例外狀況，但重點是，它會允許下一個活動執行。 此行為背後的原因是因為 PowerShell 語言所設計來處理錯誤的方式。    

執行期間可能發生的 PowerShell 錯誤類型為終止或非終止。  其差異如下所示︰

* 終止錯誤︰執行期間所發生的嚴重錯誤，此錯誤會完全終止命令 (或指令碼執行)。 範例可能包括不存在的 Cmdlet、會防止 Cmdlet 執行的語法錯誤，或其他嚴重錯誤。

* 非終止錯誤︰非嚴重錯誤，會允許繼續執行，而不理會失敗。 範例包括操作錯誤，如找不到檔案、權限問題等等。

Azure 自動化的圖形化 Runbook 已獲得改善而能夠包含錯誤處理，現在可讓您將例外狀況變成非終止錯誤，並在活動之間建立錯誤連結。 這可讓 Runbook 作者捕捉到錯誤，並獲得管理已實現或非預期狀況的途徑。  

## <a name="when-to-use"></a>使用時機

控制工作流程的執行是相當重要的機制，可確保每當有重要活動擲回錯誤或例外狀況時，您可以避免繼續進行 Runbook 中的下一個活動，並適當地處理錯誤。  當 Runbook 支援商務或服務營運流程時，尤其需要有此機制。

對於每個可能產生錯誤的活動，Runbook 作者均可新增指向任何其他活動的錯誤連結。  目的地活動可以是任何類型︰程式碼活動、叫用 Cmdlet、叫用另一個 Runbook，或任何其他項目。 

此外，目的地活動也可以具有連出連結 (可為一般連結或錯誤連結)，讓 Runbook 作者可以實作複雜的錯誤處理邏輯，而不必包括程式碼活動。  雖然建議作法是建立具有常用功能的專用錯誤處理 Runbook，但您不一定要照做，而且將錯誤處理邏輯放在 PowerShell 程式碼活動中也並非是唯一的替代方法。  

例如，請考慮嘗試啟動 VM 並在其中安裝應用程式的 Runbook，但如果 VM 未正確啟動，它會執行兩個動作︰ 

1. 傳送關於這個問題的通知。
2. 改為啟動另一個會自動佈建新 VM 的 Runbook。 

有一個解決方式是讓錯誤連結指向處理步驟 1 的活動，例如 **Write-Warning** Cmdlet，連線到步驟 2 的活動，例如 **Start-AzureRmAutomationRunbook** Cmdlet。 

您也可以讓此行為廣泛用於許多 Runbook，並將這兩個活動放在不同的錯誤處理 Runbook 中，以遵循稍早建議的指導方針。  在呼叫此錯誤處理 Runbook 之前，您可以從原始 Runbook 中的資料建構自訂訊息，然後將此訊息做為參數傳遞至錯誤處理 Runbook。 

## <a name="how-to-use"></a>使用方式

每個活動均有組態可將例外狀況變成非終止錯誤。 此組態預設為停用狀態。  請為您想要用來處理錯誤的任何活動啟用此組態。  藉由啟用此組態，您可以確保活動中的終止和非終止錯誤都會視為非終止錯誤來進行處理，然後再以錯誤連結進行處理。  進行此設定之後，您就可以建立會處理錯誤的活動。  如果活動產生任何錯誤，則會出現連出的錯誤連結，而不會出現一般連結，即使活動也產生一般輸出。<br><br> ![自動化 Runbook 的錯誤連結範例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在以下簡單範例中，我們的 Runbook 會擷取內含虛擬機器電腦名稱的變數，然後嘗試使用下一個活動啟動虛擬機器。<br><br> ![自動化 Runbook 的錯誤處理範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

依設定，**Get-AutomationVariable** 活動和 **Start-AzureRmVm** 會將例外狀況轉換為錯誤。  如果無法取得變數或啟動 VM，就會產生錯誤。<br><br> ![自動化 Runbook 的錯誤處理活動設定](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

錯誤連結會從這些活動流向單一**錯誤管理**活動 (程式碼活動)，後者已設定了簡單的 PowerShell 運算式，會使用 Throw 關鍵字與 $Error.Exception.Message 來停止處理，以取得說明目前之例外狀況的訊息。<br><br> ![自動化 Runbook 的錯誤處理程式碼範例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化 Runbook 中的連結和連結類型，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md#links-and-workflow)。

* 若要深入了解 Runbook 執行方式、如何監視 Runbook 工作，以及其他技術性詳細資料，請參閱 [追蹤 Runbook 工作](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


