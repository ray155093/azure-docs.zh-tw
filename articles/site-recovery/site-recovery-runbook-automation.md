---
title: "在 Site Recovery 中將 Azure 自動化 Runbook 新增至復原方案 | Microsoft Docs"
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
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: ac56273cf85aff550febecf0d75ec87d5c6dbbca
ms.openlocfilehash: 26547135548dde96e9da601f2e0ccfe96c626880
ms.lasthandoff: 02/23/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>將 Azure 自動化 Runbook 加入至復原計劃
本教學課程說明如何將 Azure Site Recovery 與 Azure 自動化整合在一起，以提供復原計畫的擴充性。 復原計畫可以協調使用 Azure Site Recovery 保護的虛擬機器復原，以便同時複寫至次要雲端和 Azure 案例。 復原方案也有助於讓復原「保持一致精確」、「可重複執行」及「自動化」。 如果您要將虛擬機器容錯移轉至 Azure，與 Azure 自動化整合可擴充復原計畫，並讓您能夠執行 Runbook，進而允許進行功能強大的自動化工作。

如果您還沒聽過「Azure 自動化」，請在[這裡](https://azure.microsoft.com/services/automation/)註冊，然後從[這裡](https://azure.microsoft.com/documentation/scripts/)下載其範例指令碼。 使用[這裡](https://azure.microsoft.com/blog/?p=166264)的復原計劃進一步了解 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)，以及如何將復原協調至 Azure。

在本教學課程中，我們將探討如何將 Azure 自動化 Runbook 整合到復原方案。 我們會將先前需要手動介入的簡單工作自動化，並了解如何將多步驟復原轉換成單鍵復原動作。

## <a name="customize-the-recovery-plan"></a>自訂復原方案
1. 讓我們從開啟復原方案的 [資源] 刀鋒視窗開始。 您可以看到復原方案中有兩個新增來進行復原的虛擬機器。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 按一下 [自訂] 按鈕來開始新增 Runbook。 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 在開始群組 1 上按一下滑鼠右鍵，然後選擇新增一個「新增後續動作」。
2. 選取要在新刀鋒視窗中選擇指令碼。
3. 將指令碼命名為 'Hello World'。
4. 選擇「自動化帳戶」名稱。 
    >[!NOTE]
    > 自動化帳戶可以在任何 Azure 地理位置，但必須與 Site Recovery 保存庫在相同的訂用帳戶中。
    
5. 從 [自動化帳戶] 中選取一個 Runbook。 此 Runbook 是在執行復原方案時，於復原第一個群組後執行的指令碼。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 選取 [確定] 來儲存指令碼。 指令碼會新增至 [群組 1: 開始] 的後續動作群組。

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>新增指令碼的要點
1. 您可以在指令碼上按一下滑鼠右鍵，然後選擇要 [刪除步驟] 或 [更新指令碼]。
2. 指令碼可以在從內部部署環境容錯移轉至 Azure 時，在 Azure 上執行，也可以在從 Azure 容錯回復至內部部署環境時，於關機前在 Azure 上當作主要端指令碼來執行。
3. 指令碼執行時會插入復原方案內容。

以下是內容變數外觀的範例。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


下表包含內容中每個變數的名稱和描述。

| **變數名稱** | **說明** |
| --- | --- |
| RecoveryPlanName |正在執行的計劃名稱。 此變數可協助您根據名稱和重複使用指令碼，而採取不同的動作 |
| FailoverType |指定容錯移轉是測試、已計劃，還是未計劃。 |
| FailoverDirection |指定復原是主要還是次要 |
| GroupID |識別計劃執行時復原計劃內的群組編號 |
| VmMap |群組中所有虛擬機器的陣列 |
| VMMap 索引鍵 |每個 VM 的唯一索引鍵 (GUID)。 與虛擬機器的適用 VMM ID 相同。 |
| SubscriptionId |建立 VM 的 Azure 訂用帳戶識別碼。 |
| RoleName |正在復原的 Azure VM 的名稱 |
| CloudServiceName |在其下建立虛擬機器的 Azure 雲端服務名稱。 |
| resourceGroupName|在其下建立虛擬機器的「Azure 資源群組」名稱。 |
| RecoveryPointId|VM 復原到的時間戳記。 |

您也需要確定自動化帳戶已新增下列模組。 所有模組都必須是相容版本。 簡單的做法就是確保所有模組都是最新可用的版本。
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>在迴圈中存取 VmMap 的所有 VM
使用下列程式碼片段循環存取 VmMap 的所有 VM。

```
    $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
    $vmMap = $RecoveryPlanContext.VmMap
     foreach($VMID in $VMinfo)
     {
         $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
             #this check is to ensure that we skip when some data is not available else it will fail
         Write-output "Resource group name ", $VM.ResourceGroupName
         Write-output "Rolename " = $VM.RoleName
         }
     }

```

> [!NOTE]
> 當指令碼是開機群組的前置動作時，資源群組名稱和角色名稱值會是空的。 只有當該群組中的 VM 成功地容錯移轉，而且指令碼是開機群組的後續動作時，才會填入值。

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>多個復原方案使用相同的 Automation Runbook

利用外部變數，單一指令碼可跨多個復原方案使用。 您可以使用 [Azure 自動化變數](../automation/automation-variables.md)，儲存您在復原方案執行時可傳遞的參數。 您可以在變數開頭加上復原方案的名稱，為每個復原方案建立個別變數，當做參數使用。 您可以變更參數而不需要變更指令碼，讓指令碼有不同的運作方式。

### <a name="using-simple-string-variables-in-runbook-script"></a>在 Runbook 指令碼中使用簡單的字串變數

假設指令碼會取得 NSG 的輸入，並將輸入套用至復原方案的 VM。

為了讓指令碼知道正在執行的復原方案，您可以使用「復原方案內容」。

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

若要套用現有的 NSG，您需要 NSG 名稱和 NSG 資源群組。 我們提供這些變數做為復原方案指令碼的輸入。 若要這樣做，請在自動化帳戶資產中建立兩個變數，開頭加上您想建立參數的復原方案名稱。

1. 建立變數來儲存 NSG 名稱。 開頭加上復原方案的名稱。
    ![建立 NSG 名稱變數](media/site-recovery-runbook-automation-new/var1.png)

2. 建立變數來儲存 NSG 的 RG 名稱。 開頭加上復原方案的名稱。
    ![建立 NSG RG 名稱](media/site-recovery-runbook-automation-new/var2.png)


在指令碼中，使用下列參考程式碼以取得變數的值︰

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

接著，您可以在 Runbook 中使用變數，並將 NSG 套用至容錯移轉的虛擬機器的網路介面。

```
     InlineScript { 
         if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
```

針對每個復原方案，建立獨立變數讓您可以重複使用指令碼，並在開頭加上復原方案名稱。 [這裡提供](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)此範例的完整端對端指令碼。


### <a name="using-complex-variable-to-store-more-information"></a>使用複雜變數來儲存詳細資訊

假設您只想要一個指令碼在特定 VM 上開啟公用 IP。另一個範例是在不同的虛擬機器 (並非全部) 上套用不同的 NSG。 此指令碼應該可重複用於其他任何復原方案。 每個復原方案的虛擬機器數目不一定 (例如，sharepoint 復原有兩個前端，簡單的 LOB 應用程式只有一個前端)。 若要達成這種結果，就不可能為每個復原方案建立不同的變數。 我們在此採用新的技巧，在 Azure 自動化帳戶資產中，藉由指定多個值建立[複雜變數](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)。 您需要在 Azure powershell 中執行下列步驟。

1. 在 Azure powershell 中，登入您的訂用帳戶。

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. 若要儲存參數，請建立與復原方案同名的複雜變數。

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    在此複雜變數中，**VMDetails* 是受保護虛擬機器的 VM 識別碼。 您可以在入口網站上的虛擬機器屬性中找到此資訊。 我們在此已建立變數來儲存兩部虛擬機器的詳細資料。

    ![要做為 GUID 的 VM 識別碼](media/site-recovery-runbook-automation-new/vmguid.png)

3. 在 Runbook 中使用此變數，如果在復原方案內容中找到任何給定的 VMGUID，就將 NSG 套用在虛擬機器上。

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. 在 Runbook 中，循環查看復原方案內容的 VM，檢查 VM 是否也存在於 **$VMDetailsObj** 中。 如果存在的話，則存取變數的屬性來套用 NSG。
    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap
           
        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID
            
            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

您可以將相同的指令碼用於不同的復原方案，將對應至不同復原方案的儲存儲存在不同變數中，就能提供不同的參數。

## <a name="sample-scripts"></a>範例指令碼
如需您可以直接匯入自動化帳戶中的指令碼儲存機制，請參閱 [Kristian Nese 的 OMS 指令碼儲存機制](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

這裡的指令碼是會部署以下所有指令碼的 Azure Resource Manager 範本

* NSG

NSG Runbook 會指派公用 IP 位址給復原方案內的每個 VM，並將其虛擬網路介面卡連接至允許預設通訊的網路安全性群組

* PublicIP

公用 IP Runbook 會指派公用 IP 位址給復原方案內的每個 VM。 是否能夠存取機器和應用程式，將取決於每個客體內的防火牆設定

* CustomScript

CustomScript Runbook 會指派公用 IP 位址給復原方案內的每個 VM，並安裝一個自訂指令碼擴充功能，此擴充功能會提取您在部署範本期間所參考的指令碼

* NSGwithCustomScript

NSGwithCustomScript Runbook 會指派公用 IP 位址給復原方案內的每個 VM、使用擴充功能來安裝自訂指令碼，以及將虛擬網路介面卡連接至允許遠端存取之預設連入和連出通訊的 NSG

## <a name="additional-resources"></a>其他資源
[Azure 自動化服務執行身分帳戶](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 自動化概觀](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自動化概觀")

[Azure 自動化範例指令碼](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 自動化範例指令碼")

