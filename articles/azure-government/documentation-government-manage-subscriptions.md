---
title: "Azure Government 訂用帳戶 | Microsoft Docs"
description: "在 Azure Government 中管理訂用帳戶的相關資訊"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>在 Azure Government 中管理和連接至訂用帳戶
Azure Government 具有用於管理環境的唯一 URL 和端點。 請務必使用正確的連接，透過入口網站或 PowerShell 來管理環境。 連接至 Azure Government 環境之後，如果已部署元件，則用於管理服務的正常作業就會運作。

## <a name="connecting-via-the-portal"></a>透過入口網站連接
入口網站是大多數人連接至 Azure Government 的主要方式。  若要連接，請瀏覽至入口網站，網址為 [https://portal.azure.us](https://portal.azure.us)。  舊版 Azure 入口網站可以透過 [https://manage.windowsazure.us](https://manage.windowsazure.us) 進行存取。

連接至 [https://account.windowsazure.us](https://account.windowsazure.us)，即可建立您帳戶的訂用帳戶。

## <a name="connecting-via-powershell"></a>透過 PowerShell 連接
不論您透過指令碼或 Azure 入口網站中目前無法使用的存取功能以使用 Azure PowerShell 來管理大型訂用帳戶，都需要連接至 Azure Government，而非 Azure 公用。  如果您是在 Azure 公用中使用 PowerShell ，則幾乎相同。  Azure Government 中的差異如下︰

* 連接帳戶
* 區域名稱

> [!NOTE]
> 如果您尚未使用 PowerShell，請參閱 [Azure PowerShell 簡介](/powershell/azureps-cmdlets-docs)。
> 
> 

當您啟動 PowerShell 時，必須指定環境參數，以告訴 Azure PowerShell 連接至 Azure Government。  此參數確保 PowerShell 連接至正確的端點。  當您登入帳戶時，會決定端點集合。  不同的 API 需要不同版本的環境參數︰

| 連線類型 | 命令 |
| --- | --- |
| [服務管理](https://msdn.microsoft.com/library/dn708504.aspx)命令 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [資源管理](https://msdn.microsoft.com/library/mt125356.aspx)命令 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 命令 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory 命令第 2 版](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 命令列](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

如果使用 New-AzureStorageContext 連接至儲存體帳戶，並指定 AzureUSGovernment，則也可能會使用 Environment 參數。

### <a name="determining-region"></a>判斷區域
連接之後，還有一項差異 – 用來將目標設為服務的區域。  每個 Azure 雲端都有不同的區域。  服務可用性頁面上會列出它們。  您通常會使用命令之 Location 參數中的區域。

需要考量一個項目。  Azure Government 區域的格式化方式必須與一般名稱不同︰

| 一般名稱 | 命令 |
| --- | --- |
| 美國政府維吉尼亞州 |美國政府維吉尼亞州 |
| 美國政府愛荷華州 |美國政府愛荷華州 |

> [!NOTE]
> 使用位置參數時，US 與 Gov 之間沒有空格。
> 
> 

如果您想要驗證 Azure Government 中可用的區域，可以執行下列命令，然後列印目前清單︰

    Get-AzureLocation

如果您想要知道 Azure 的可用環境，可以執行︰

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>透過 Visual Studio 連線
Visual Studio 可供開發人員在建置解決方案時輕鬆地管理其 Azure 訂用帳戶。  Visual Studio 目前不允許您在使用者介面中設定連往 Azure Government 的連線。  

### <a name="updating-visual-studio-for-azure-government"></a>更新 Azure Government 的 Visual Studio
若要讓 Visual Studio 能夠連線至 Azure Government，您需要更新登錄。

1. 關閉 Visual Studio
2. 建立名為 **VisualStudioForAzureGov.reg** 的文字檔
3. 複製下列文字並貼到 **VisualStudioForAzureGov.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 儲存檔案，然後按兩下以執行檔案。  系統會提示您將檔案合併到您的登錄。
5. 啟動 Visual Studio，並開始使用[雲端總管](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> 設定了此登錄機碼後，便只有 Azure Government 訂用帳戶可供存取。  您仍然可以看到之前設定的訂用帳戶，但它們無法運作，因為 Visual Studio 現在是連線到 Azure Government 而不是 Azure 公用。  請參閱下一節，以取得用來還原變更的步驟。
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>還原 Visual Studio 對 Azure Government 的連線
若要讓 Visual Studio 能夠連線到 Azure 公用，您需要移除啟用 Azure Government 連線的登錄設定。

1. 關閉 Visual Studio
2. 建立名為 **VisualStudioForAzureGov_Remove.reg** 的文字檔
3. 複製下列文字並貼到 **VisualStudioForAzureGov_Remove.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 儲存檔案，然後按兩下以執行檔案。  系統會提示您將檔案合併到您的登錄。
5. 啟動 Visual Studio

> [!NOTE]
> 此登錄機碼還原後，Azure Government 訂用帳戶會顯示，但無法加以存取。  您可以放心地移除它們。
> 
> 

## <a name="next-steps"></a>後續步驟
如果您是尋找詳細資訊，則可以查看：

* [GitHub 上的 PowerShell 文件](https://github.com/Azure/azure-powershell)
* [連接至資源管理的逐步指示](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [MSDN 上的 Azure PowerShell 文件](https://msdn.microsoft.com/library/mt619274.aspx)

如需補充資訊和更新，請訂閱 [Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


