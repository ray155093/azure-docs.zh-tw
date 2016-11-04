---
title: 開始使用 Azure Batch PowerShell | Microsoft Docs
description: 取得您可以用來管理 Azure Batch 服務之 Azure PowerShell Cmdlet 的快速簡介
services: batch
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 07/28/2016
ms.author: danlep

---
# 開始使用 Azure Batch PowerShell Cmdlet
使用 Azure Batch PowerShell Cmdlet，您可以執行與您使用 Batch API、Azure 入口網站和 Azure 命令列介面 (CLI) 執行的許多相同工作並撰寫其指令碼。本文會快速介紹可用來管理 Batch 帳戶和使用批次資源 (例如集區、作業和和工作) 的 Cmdlet。本文是根據 Azure PowerShell 1.6.0 版中的 Cmdlet 所撰寫。

如需批次 Cmdlet 和詳細 Cmdlet 語法的完整清單，請參閱 [Azure 批次 Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)。

## 先決條件
* **Azure PowerShell** - 請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 以取得下載和安裝 Azure PowerShell 的指示。
  
  * 因為 Azure 批次 Cmdlet 隨附在 Azure Resource Manager 模組中，您必須執行 **Login-AzureRmAccount** Cmdlet 才能連線到訂用帳戶。
  * 建議您經常更新您的 Azure PowerShell 以利用服務更新和增強功能。
* **向 Batch 提供者命名空間註冊 (一次性操作)**：在您可以使用 Batch 帳戶之前，必須向 Batch 提供者命名空間註冊。每個訂用帳戶只需要執行這項作業一次。執行下列 Cmdlet：
  
        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch

## 管理 Batch 帳戶和金鑰
### 建立批次帳戶：
**New-AzureRmBatchAccount** 將在指定的資源群組中建立新的 Batch 帳戶。如果您還沒有資源群組，請執行 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) Cmdlet 建立一個，並在 **Location** 參數中指定其中一個 Azure 區域，例如"Central US"。例如：

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


然後，在資源群組中建立新的 Batch 帳戶，為 <account\_name> 中的帳戶指定名稱，以及指定資源群組的位置和名稱。建立 Batch 帳戶可能需要一些時間來完成。例如：

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [!NOTE]
> Batch 帳戶名稱對資源群組的 Azure 區域必須是唯一的、包含 3 到 24 個字元，並只使用小寫字母和數字。
> 
> 

### 取得帳戶存取金鑰
**Get-AzureRmBatchAccountKeys** 將顯示與 Azure Batch 帳戶相關聯的存取金鑰。例如，執行下列命令以取得您所建立帳戶的主要和次要金鑰。

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### 產生新的存取金鑰
**New-AzureRmBatchAccountKey** 將為 Azure Batch 帳戶產生新的主要或次要帳戶金鑰。例如，若要為您的 Batch 帳戶產生新的主要金鑰，請輸入：

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [!NOTE]
> 若要產生新的次要金鑰，請將 **KeyType** 參數指定為「次要」。您必須個別重新產生主要和次要金鑰。
> 
> 

### 刪除 Batch 帳戶
**Remove-AzureRmBatchAccount** 將刪除 Batch 帳戶。例如：

    Remove-AzureRmBatchAccount -AccountName <account_name>

出現提示時，請確認您想要移除的帳戶。帳戶移除可能需要一些時間來完成。

## 建立 BatchAccountContext 物件
若要在建立和管理 Batch 集區、作業、工作和其他資源時使用 Batch PowerShell Cmdlet 來進行驗證，請先建立 BatchAccountContext 物件以儲存帳戶名稱和金鑰：

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

您必須將 BatchAccountContext 物件傳遞給使用 **BatchContext** 參數的 Cmdlet。

> [!NOTE]
> 依預設，帳戶的主要金鑰用於驗證，但是您可以透過變更 BatchAccountContext 物件的 **KeyInUse** 屬性，明確地選取要使用的金鑰：`$context.KeyInUse = "Secondary"`。
> 
> 

## 建立和修改批次資源
請使用 **New-AzureBatchPool**、**New-AzureBatchJob** 和 **New-AzureBatchTask** 之類的 Cmdlet 在 Batch 帳戶下建立資源。要更新現有資源的屬性，有對應的 **Get-** 和 **Set-** Cmdlet ，要移除 Batch 帳戶下的資源，則有 **Remove-** Cmdlet。

使用許多這類 Cmdlet 時，除了傳遞 BatchContext 物件，您還需要建立或傳遞包含詳細資源設定的物件，如下列範例所示。如需其他範例，請參閱每個 Cmdlet 的詳細說明。

### 建立 Batch 集區
建立或更新 Batch 集區時，請選取雲端服務組態或計算節點上作業系統的虛擬機器組態 (請參閱 [Batch 功能概觀](batch-api-basics.md#pool))。您的選擇會決定計算節點是否會以其中一個 [Azure 客體 OS 版本](../cloud-services/cloud-services-guestos-update-matrix.md#releases)或以 Azure Marketplace 中其中一個支援的 Linux 或 Windows VM 映像製作映像。

當您執行 **New-AzureBatchPool** 時，請在 PSCloudServiceConfiguration 或 PSVirtualMachineConfiguration 物件中傳遞作業系統設定。例如，下列 Cmdlet 會使用雲端服務組態中的小型計算節點建立新的 Batch 集區，並以最新的系列 3 (Windows Server 2012) 作業系統版本製作映像。在此，**CloudServiceConfiguration** 參數會指定 $configuration 變數做為 PSCloudServiceConfiguration 物件。**BatchContext** 參數會將先前定義的變數 $context 指定為 BatchAccountContext 物件。

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

新集區中計算節點的目標數目是由自動調整公式決定。在此案例中，此公式僅僅是 **$TargetDedicated=4**，表示集區中的計算節點數最多為 4 個。

## 查詢集區、作業、工作及其他詳細資料
使用 Cmdelt (例如 **Get-AzureBatchPool**、**Get-AzureBatchJob** 和 **Get-AzureBatchTask**) 查詢在 Batch 帳戶下建立的實體。

### 查詢資料
例如，使用 **Get-AzureBatchPools** 尋找您的集區。依預設，這將查詢您帳戶下的所有集區，並假設您已經將 BatchAccountContext 物件儲存在 *$context* 中：

    Get-AzureBatchPool -BatchContext $context

### 使用 OData 篩選
您可以提供**篩選**參數給 OData 篩選，只尋找您感興趣的物件。例如，您可以找到識別碼以 “myPool” 開頭的所有集區：

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


雖然這個方法比在本機管線中使用 “Where-Object” 較不具有彈性，不過查詢將直接傳送進 Batch 服務，讓所有篩選在伺服器端運作，進而省下網際網路頻寬。

### 使用識別碼參數
使用 **Id** 參數可做為 OData 篩選器的替代方式。若要查詢識別碼為 "myPool" 的特定集區：

    Get-AzureBatchPool -Id "myPool" -BatchContext $context


**Id** 參數僅支援完整識別碼的搜尋，不能使用萬用字元或 OData 樣式的篩選器。

### 使用 MaxCount 參數
依預設，每個 Cmdlet 最多傳回 1000 個物件。如果到達此限制，請調整您的篩選器以傳回較少的物件，或使用 **MaxCount** 參數明確地設定最大值。例如：

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

若要移除上限，將 **MaxCount** 設定為 0 或更少。

### 使用管線
Batch Cmdlet 可以利用 PowerShell 管線在 Cmdlet 之間傳送資料。這和指定參數有相同效果，但讓列出多個實體更容易。例如，下列可找出您帳戶下的所有工作：

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## 後續步驟
* 如需詳細的 Cmdlet 語法和範例，請參閱 [Azure Batch Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)。
* 如需減少項目數和針對 Batch 查詢所傳回之資訊類型的詳細資訊，請參閱[有效率地查詢 Batch 服務](batch-efficient-list-queries.md)。

<!---HONumber=AcomDC_0803_2016-->