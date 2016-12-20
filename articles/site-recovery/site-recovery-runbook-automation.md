---
title: "將 Azure 自動化 Runbook 加入至復原計劃 | Microsoft Docs"
description: "本文說明 Azure Site Recovery 現在讓您使用 Azure 自動化擴充復原計畫，以便在復原至 Azure 期間，完成複雜的工作"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/23/2016
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 9f9a71cd7c6688dfd42dcb7ce52847f2016daf58


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>將 Azure 自動化 Runbook 加入至復原計劃
本教學課程說明如何將 Azure Site Recovery 與 Azure 自動化整合在一起，以提供復原計畫的擴充性。 復原計畫可以協調使用 Azure Site Recovery 保護的虛擬機器復原，以便同時複寫至次要雲端和 Azure 案例。 復原方案也有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 如果您要將虛擬機器容錯移轉至 Azure，與 Azure 自動化整合可擴充復原計畫，並讓您能夠執行 Runbook，進而允許進行功能強大的自動化工作。

如果您還沒聽過「Azure 自動化」，請在[這裡](https://azure.microsoft.com/services/automation/)註冊，然後從[這裡](https://azure.microsoft.com/documentation/scripts/)下載其範例指令碼。 深入了解 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)，以及如何使用[這裡](https://azure.microsoft.com/blog/?p=166264)的復原方案來協調復原至 Azure 的流程。

在本教學課程中，我們將會探討如何將 Azure 自動化 Runbook 整合到復原計畫。 我們會將先前需要手動介入的簡單工作自動化，並了解如何將多步驟復原轉換成單鍵復原動作。 我們也將探討簡單的指令碼發生錯誤時如何進行疑難排解。

## <a name="customize-the-recovery-plan"></a>自訂復原方案
1. 讓我們從開啟復原方案的 [資源] 刀鋒視窗開始。 您可以看到復原方案中有兩個新增來進行復原的虛擬機器。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 按一下 [自訂] 按鈕來開始新增 Runbook。 這會開啟復原方案 [自訂] 刀鋒視窗。

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 在開始群組 1 上按一下滑鼠右鍵，然後選取要新增一個「新增後續動作」。
2. 選取要在新刀鋒視窗中選擇指令碼。
3. 將指令碼命名為 'Hello World'。
4. 選擇「自動化帳戶」名稱。 這是「Azure 自動化」帳戶。 請注意，此帳戶可以在任何 Azure 地理位置，但必須與 Site Recovery 保存庫在相同的訂用帳戶中。
5. 從 [自動化帳戶] 中選取一個 Runbook。 這是將在執行復原方案時，於復原第一個群組後執行的指令碼。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 選取 [確定] 來儲存指令碼。 這會將指令碼新增到 [群組 1: 開始] 的後續動作群組中。

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>新增指令碼的要點
1. 您可以在指令碼上按一下滑鼠右鍵，然後選擇要 [刪除步驟] 或 [更新指令碼]。
2. 指令碼可以在從內部部署環境容錯移轉至 Azure 時，在 Azure 上執行，也可以在從 Azure 容錯回復至內部部署環境時，於關機前在 Azure 上當作主要端指令碼來執行。
3. 當指令碼執行時，它會插入復原方案內容。

以下是內容變數外觀的範例。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


下表包含內容中每個變數的名稱和描述。

| **變數名稱** | **說明** |
| --- | --- |
| RecoveryPlanName |正在執行的計劃名稱。 協助您根據名稱使用相同的指令碼採取動作 |
| FailoverType |指定容錯移轉是測試、已計劃，還是未計劃。 |
| FailoverDirection |指定復原是主要還是次要 |
| GroupID |識別計劃執行時復原計劃內的群組編號 |
| VmMap |群組中所有虛擬機器的陣列 |
| VMMap 索引鍵 |每個 VM 的唯一索引鍵 (GUID)。 與虛擬機器的適用 VMM ID 相同。 |
| RoleName |正在復原的 Azure VM 的名稱 |
| CloudServiceName |在其下建立虛擬機器的 Azure 雲端服務名稱。 |
| CloudServiceName (在 Resource Manager 部署模型中) |在其下建立虛擬機器的「Azure 資源群組」名稱。 |

## <a name="using-complex-variables-per-recovery-plan"></a>依復原方案使用複雜變數
有時，Runbook 所需的資訊不僅僅是 RecoveryPlanContext。 沒有任何可將參數傳遞給 Runbook 的優先機制。 不過，如果您想要透過多個復原方案使用相同的指令碼，您可以使用「復原方案內容」變數 'RecoveryPlanName' 和使用以下的實驗性技術，在 Runbook 中使用「Azure 自動化」複雜變數。 以下範例說明如何建立三種不同的複雜變數資產，然後根據復原方案的名稱在 Runbook 中使用它們。

想像您要在 Runbook 中使用 3 個額外的參數。 讓我們將這些參數編碼成 JSON 格式 {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}

使用 [AA 複雜變數](../automation/automation-variables.md#variable-types)來建立新的「自動化」資產。
將變數命名為 <RecoveryPlanName>-params。
您可以使用這裡的參考來建立[複雜變數](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)。

針對不同的復原方案，以下列方式來命名變數

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

現在，在指令碼中，以下列方式來參考參數

1. 從 $rpname = $Recoveryplancontext 變數取得 RP 名稱
2. 取得 $paramValue = "$($rpname)-params" 的資產
3. 透過呼叫 Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue，使用這個作為復原方案的複雜變數。

舉例來說，若要取得 SharepointApp 復原方案的複雜變數/參數，請建立名為 'SharepointApp-params' 的「Azure 自動化」複雜變數。

透過使用 Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue 陳述式從資產中擷取變數，在復原方案中使用它。 [如需詳細資訊，請參考本文](https://msdn.microsoft.com/library/dn913772.aspx)

如此一來，就可以透過在資產中儲存方案特定的複雜變數，將相同的指令碼用於不同的復原方案。

## <a name="sample-scripts"></a>範例指令碼
如需您可以直接匯入到自動化帳戶中的指令碼儲存機制，請參閱 [Kristian Nese 的 OMS 指令碼儲存機制](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

這裡的指令碼是會部署以下所有指令碼的 Azure Resource Manager 範本

* NSG

NSG Runbook 會指派「公用 IP」位址給「復原方案內的每個 VM，並將其虛擬網路介面卡連接至會允許預設通訊的「網路安全性群組」

* PublicIP

PublicIP Runbook 會指派「公用 IP」位址給「復原方案」內的每個 VM。 是否能夠存取機器和應用程式，將取決於每個客體內的防火牆設定

* CustomScript

CustomScript Runbook 會指派「公用 IP」位址給「復原方案」內的每個 VM，並安裝一個自訂指令碼擴充功能，此擴充功能會提取您在部署範本期間所參考的指令碼

* NSGwithCustomScript

NSGwithCustomScript Runbook 會指派「公用 IP」位址給「復原方案」內的每個 VM、使用擴充功能來安裝自訂指令碼，以及將虛擬網路介面卡連接至允許遠端存取之預設連入和連出通訊的 NSG

## <a name="additional-resources"></a>其他資源
[Azure 自動化服務執行身分帳戶](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 自動化概觀](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自動化概觀")

[Azure 自動化範例指令碼](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 自動化範例指令碼")



<!--HONumber=Nov16_HO3-->


