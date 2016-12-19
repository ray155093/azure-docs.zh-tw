---
title: "編譯 Azure 自動化 DSC 中的組態 | Microsoft Docs"
description: "「期望的狀態設定 (DSC)」組態兩種編譯方式的概觀：在 Azure 入口網站中，以及使用 Windows PowerShell。 "
services: automation
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 01/25/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d64ee97acdc1fb43d8f1d998c826bd6e25935e0a


---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>編譯 Azure 自動化 DSC 中的組態
使用 Azure 自動化時有兩種方式可以編譯「期望的狀態設定 (DSC)」組態：在 Azure 入口網站中，以及使用 Windows PowerShell。 下表將協助您根據方法的特性判斷何時應使用哪種方法： 

### <a name="azure-preview-portal"></a>Azure Preview 入口網站
* 互動式使用者介面的最簡單方法
* 提供簡單參數值的表單
* 輕鬆追蹤工作狀態
* 使用 Azure 登入資訊驗證存取

### <a name="windows-powershell"></a>Windows PowerShell
* 使用 Windows PowerShell Cmdlet 從命令列呼叫
* 可以包含在具有多個步驟的自動化解決方案中
* 提供簡單和複雜的參數值
* 追蹤工作狀態
* 支援 PowerShell Cmdlet 所需的用戶端
* 傳遞 ConfigurationData
* 編譯使用認證的組態

在您決定編譯方法後，您可以依照下列個別的程序開始編譯。

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>使用 Azure 入口網站編譯 DSC 組態
1. 從您的自動化帳戶中，按一下 [組態] 。
2. 按一下組態以開啟其刀鋒視窗。
3. 按一下 [編譯] 。
4. 如果組態沒有參數，系統會提示您確認是否要加以編譯。 如果組態有參數，即會開啟 [編譯組態] 刀鋒視窗，讓您可以提供參數值。 如需參數的進一步詳細資訊，請參閱以下的<a href="#basic-parameters">**基本參數**</a>一節。
5. [編譯工作]  刀鋒視窗隨即開啟，供您追蹤編譯工作的狀態，以及因為此工作而放在 Azure 自動化 DSC 提取伺服器上的節點組態 (MOF 組態文件)。

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>使用 Windows PowerShell 編譯 DSC 組態
您可以在 Windows PowerShell 中使用 [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) 開始編譯。 下列範例程式碼會啟動 DSC 組態 **SampleConfig**的編譯。

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 

`Start-AzureRmAutomationDscCompilationJob` 會傳回可用來追蹤工作狀態的編譯工作物件。 接著，您可以使用此編譯工作物件與 [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) 來判斷編譯工作的狀態，並使用 [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) 來檢視其串流 (輸出)。 下列範例程式碼會啟動 **SampleConfig** 組態的編譯，並在編譯完成後顯示其串流。

    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)           
    {
        $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
        Start-Sleep -Seconds 3
    }

    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


## <a name="basic-parameters"></a>基本參數
DSC 組態中的參數宣告 (包括參數類型和屬性) 的運作方式與 Azure 自動化 Runbook 中相同。 若要深入了解 Runbook 參數，請參閱 [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 。

下列範例使用兩個名為 **FeatureName** 和 **IsPresent** 的參數，來判斷在編譯期間產生的 **ParametersExample.sample** 節點組態中的屬性值。

    Configuration ParametersExample
    {
        param(
            [Parameter(Mandatory=$true)]

            [string] $FeatureName,

            [Parameter(Mandatory=$true)]
            [boolean] $IsPresent
        )

        $EnsureString = "Present"
        if($IsPresent -eq $false)
        {
            $EnsureString = "Absent"
        }

        Node "sample"
        {
            WindowsFeature ($FeatureName + "Feature")
            {
                Ensure = $EnsureString
                Name = $FeatureName
            }
        }
    }

您可以在 Azure Automation DSC 入口網站或 Azure PowerShell 中編譯使用基本參數的 DSC 組態：

### <a name="portal"></a>入口網站
在入口網站中，您可以在按一下 [編譯] 後輸入參數值。

![替代文字](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell
PowerShell 會要求您將參數放入 [hashtable](http://technet.microsoft.com/library/hh847780.aspx) 中，且其中的索引鍵必須符合參數名稱，值等於參數值。

    $Parameters = @{
            "FeatureName" = "Web-Server"
            "IsPresent" = $False
    }


    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 


如需如何將 PSCredentials 傳入作為參數的相關資訊，請參閱下方的 <a href="#credential-assets">**認證資產**</a> 。

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** 可讓您在使用 PowerShell DSC 時區隔結構化組態與任何環境特定組態。 請參閱 [區隔 PowerShell DSC 中的 "What" 與 "Where"](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) ，以深入了解 **ConfigurationData**。

> [!NOTE]
> 使用 Azure PowerShell 在 Azure 自動化 DSC 中進行編譯時可以使用 **ConfigurationData** ，但使用 Azure 入口網站時則否。
> 
> 

下列範例 DSC 組態會透過 **$ConfigurationData** 和 **$AllNodes** 關鍵字來使用 **ConfigurationData**。 在此範例中也需要 [**xWebAdministration** 模組](https://www.powershellgallery.com/packages/xWebAdministration/)：

     Configuration ConfigurationDataSample
     {
        Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

        Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 

        Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
        {
            xWebsite Site
            {
                Name = $Node.SiteName
                PhysicalPath = $Node.SiteContents
                Ensure   = "Present"
            }
        }
    }

您可以使用 PowerShell 編譯上述 DSC 組態。 PowerShell 會將以下兩個節點組態新增至 Azure Automation DSC 提取伺服器：**ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**：

    $ConfigData = @{
        AllNodes = @(
            @{
                NodeName = "MyVM1"
                Role = "WebServer"
            },
            @{
                NodeName = "MyVM2"
                Role = "SQLServer"
            },
            @{
                NodeName = "MyVM3"
                Role = "WebServer"

            }

        )

        NonNodeData = @{
            SomeMessage = "I love Azure Automation DSC!"

        }

    } 

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


## <a name="assets"></a>資產
Azure 自動化 DSC 組態和 Runbook 中的資產參考是相同的。 如需詳細資訊，請參閱下列主題：

* [憑證](automation-certificates.md)
* [連線](automation-connections.md)
* [認證](automation-credentials.md)
* [變數](automation-variables.md)

### <a name="credential-assets"></a>認證資產
雖然 Azure 自動化中的 DSC 組態可以使用 **Get-AzureRmAutomationCredential**參考認證資產，但如有需要，也可以透過參數傳入認證資產。 如果組態採用屬於 **PSCredential** 類型的參數，您必須將 Azure 自動化認證資產的字串名稱傳遞為該參數的值，而不是 PSCredential 物件。 具有該名稱的 Azure 自動化認證資產會在背景中被擷取，並傳遞至組態。

要在節點組態 (MOF 組態文件) 中保持認證的安全性，需要在節點組態 MOF 檔案中為認證加密。 Azure 自動化會進一步執行此步驟，而加密整個 MOF 檔案。 不過，目前您必須告知 PowerShell DSC 在節點組態 MOF 產生期間以純文字形式輸出認證是可行的，因為 PowerShell DSC 並不知道在透過編譯工作產生 MOF 檔案之後 Azure 自動化會加密整個檔案。

您可以告知 PowerShell DSC，使用 <a href="#configurationdata">**ConfigurationData**</a>。 您應針對每個出現在 DSC 組態中且使用認證的節點區塊名稱，透過 **ConfigurationData** 傳遞 `PSDscAllowPlainTextPassword = $true`。

下列範例說明使用自動化認證資產的 DSC 組態。

    Configuration CredentialSample
    {
       $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"

        Node $AllNodes.NodeName
        { 
            File ExampleFile
            { 
                SourcePath = "\\Server\share\path\file.ext" 
                DestinationPath = "C:\destinationPath" 
                Credential = $Cred 
               }
        }
    }

您可以使用 PowerShell 編譯上述 DSC 組態。 PowerShell 會將以下兩個節點組態新增至 Azure Automation DSC 提取伺服器：**CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。

    $ConfigData = @{
        AllNodes = @(
            @{
                NodeName = "*"
                PSDscAllowPlainTextPassword = $True
            },
            @{
                NodeName = "MyVM1"
            },
            @{
                NodeName = "MyVM2"
            }
        )
    }

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData



<!--HONumber=Nov16_HO3-->


