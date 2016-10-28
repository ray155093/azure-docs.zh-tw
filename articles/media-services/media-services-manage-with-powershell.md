<properties 
	pageTitle="利用 PowerShell 管理 Azure Media Services 帳戶" 
	description="瞭解如何管理 PowerShell cmdlet 與 Azure Media Services 帳戶。" 
	authors="Juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2016"
	ms.author="juliako"/>


#利用 PowerShell 管理 Azure Media Services 帳戶

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] 若要建立 Azure 媒體服務帳戶，您必須擁有 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

##Overview 

本文列出 Azure 媒體服務 (AMS) 在 Azure Resource Manager 架構中的 Azure PowerShell Cmdlet。Cmdlet 存在於 **Microsoft.Azure.Commands.Media** 命名空間。

## 版本

**ApiVersion**："2015-10-01"
               

## New-AzureRmMediaService

建立媒體服務。

### 語法

參數集︰StorageAccountIdParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

參數集︰StorageAccountsParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 | 無
---|---
必要？ | true
位置？ | 0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |Name
---|---
必要？ |true
位置？ |1
預設值 |無
接受管線輸入？ |false
接受萬用字元？ |false

**-Location &lt;字串&gt;**

指定媒體服務的資源位置。

別名 |無
---|---
必要？ |true
位置？ |2
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-StorageAccountId &lt;字串&gt;**

指定與媒體服務相關聯的主要儲存體帳戶。

- 只支援新的儲存體帳戶 (使用 Resource Manager API 所建立)。

- 儲存體帳戶必須存在，而且具有與媒體服務相同的位置。

別名 |無
---|---
必要？ |true
位置？ |3
預設值 |無
接受管線輸入？ |true(ByPropertyName)
參數集名稱 |StorageAccountIdParamSet
接受萬用字元？|false

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

指定與媒體服務相關聯的儲存體帳戶。

- 只支援新的儲存體帳戶 (使用 Resource Manager API 所建立)。

- 儲存體帳戶必須存在，而且具有與媒體服務相同的位置。

- 只可以指定一個主要儲存體帳戶。

別名 |無
---|---
必要？ |true
位置？ |3
預設值 |無
接受管線輸入？ |true(ByPropertyName)
參數集名稱 |StorageAccountsParamSet
接受萬用字元？ |false

**-Tags &lt;雜湊表&gt;**

指定與媒體服務相關聯之標記的雜湊表。

- 範例：@{"tag1"="value1";"tag2"=:value2"}

別名 |無
---|---
必要？ |false
位置？ |已命名
預設值 |無
接受管線輸入？ |false
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Set-AzureRmMediaService

更新媒體服務。

### 語法

	Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |Name
---|---
必要？ |True
位置？ |1
預設值 |None
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |False

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

指定與媒體服務相關聯的儲存體帳戶。

- 只支援新的儲存體帳戶 (使用 Resource Manager API 所建立)。

- 儲存體帳戶必須存在，而且具有與媒體服務相同的位置。

- 只可以指定一個主要儲存體帳戶。

別名 |無
---|---
必要？ |false
位置？ |已命名
預設值 |無
接受管線輸入？ |true(ByPropertyName)
參數集名稱 |StorageAccountsParamSet
接受萬用字元？ |false

**-Tags &lt;雜湊表&gt;**

指定與此媒體服務相關聯之標記的雜湊表。

- 與媒體服務相關聯的標記會取代為客戶指定的值。

別名 |無
---|---
必要？ |False
位置？ |已命名
預設值 |None
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Remove-AzureRmMediaService

移除媒體服務。

### 語法

	Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
必要？ |true
位置？ |2
預設值 |None
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |False

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Get-AzureRmMediaService

取得資源群組中的所有媒體服務或取得指定名稱的媒體服務。

### 語法

參數集：ResourceGroupParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]	

參數集：AccountNameParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
參數集名稱 |ResourceGroupParameterSet、AccountNameParameterSet
接受萬用字元？false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
必要？ |true
位置？ |1
預設值 |無
接受管線輸入？ |true(ByPropertyName)
參數集名稱 |AccountNameParameterSet
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Get-AzureRmMediaServiceKeys

取得媒體服務的金鑰。

### 語法

	Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
必要？ |true
位置？ |1
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Set-AzureRmMediaServiceKey

重新產生媒體服務的主要或次要金鑰。

### 語法

	Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
必要？ |true
位置？ |1
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-KeyType &lt;KeyType&gt;**

指定媒體服務的金鑰類型。

- 主要或次要

別名 |無
---|---
必要？ |true
位置？ |2
預設值 |無
接受管線輸入？ |false
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## Sync-AzureRmMediaServiceStorageKeys

同步處理與媒體服務相關聯之儲存體帳戶的儲存體帳戶金鑰。

### 語法

	Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### 參數

**-ResourceGroupName &lt;字串&gt;**

指定此媒體服務所屬資源群組的名稱。

別名 |無
---|---
必要？ |true
位置？ |0
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-AccountName &lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
必要？ |true
位置？ |1
預設值 |無
接受管線輸入？ |true(ByPropertyName)
接受萬用字元？ |false

**-StorageAccountId &lt;字串&gt;**

指定與媒體服務相關聯的儲存體帳戶。

別名 |識別碼
---|---
必要？ |true
位置？ |2
預設值 |無
接受管線輸入？ | true(ByPropertyName)
接受萬用字元？ |false

**&lt;CommandParameters&gt;**

這個 Cmdlet 支援一般參數：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### 輸入

輸入類型是可以透過管線傳送至 Cmdlet 的物件類型。

### 輸出

輸出類型是 Cmdlet 所發出的物件類型。

## 後續步驟 

查看媒體服務學習途徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!----HONumber=AcomDC_0907_2016-->