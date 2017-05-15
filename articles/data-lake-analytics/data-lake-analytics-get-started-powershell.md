---
title: "透過 Azure PowerShell 開始使用 Azure Data Lake Analytics | Microsoft Docs"
description: "使用 Azure PowerShell 建立 Data Lake Analytics 帳戶、使用 U-SQL 建立 Data Lake Analytics 作業，以及提交作業。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>教學課程：透過 Azure PowerShell 開始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure PowerShell 建立 Azure Data Lake Analytics 帳戶，然後提交和執行 U-SQL 作業。 如需有關 Data Lake Analytics 的詳細資訊，請參閱 [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列資訊：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **具有 Azure PowerShell 的工作站**。 請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a name="preparing-for-the-tutorial"></a>準備教學課程
若要建立 Data Lake Analytics 帳戶，您必須先定義：

* **Azure 資源群組**：Data Lake Analytics 帳戶必須建立在 Azure 資源群組內。
* **Data Lake Analytics 帳戶名稱**：Data Lake 帳戶名稱只能包含小寫字母和數字。
* **位置**：其中一個支援 Data Lake Analytics 的 Azure 資料中心。
* **預設 Data Lake Store 帳戶**：每個 Data Lake Analytics 帳戶都有一個預設的 Data Lake Store 帳戶。 這些帳戶必須位於相同的位置。

本教學課程中的 PowerShell 程式碼片段會使用這些變數來儲存此資訊

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶

如果您還沒有可使用的資源群組，請建立一個。 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

每個 Data Lake Analytics 帳戶都需要預設 Data Lake Store 帳戶，用於儲存記錄。 您可以重複使用現有的帳戶，或建立新的帳戶。 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

資源群組和 Data Lake Store 帳戶一旦可用，請建立 Data Lake Analytics 帳戶。

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>取得 Data Lake Analytics 帳戶的相關資訊

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>提交 U-SQL 作業

建立含有下列 U-SQL 指令碼的文字檔。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

提交指令碼。

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>監視 U-SQL 作業

列出帳戶中的所有作業。 輸出包含目前執行中作業和最近完成的作業。

```
Get-AdlJob -Account $adla
```

取得特定作業的狀態。

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

您可以使用 Wait-AdlJob Cmdlet，而不需在作業完成前反覆呼叫 Get-AdlAnalyticsJob。

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

在作業完成後，藉由列出資料夾中的檔案，檢查輸出檔案是否是否。

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

檢查檔案是否存在。

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>上傳和下載檔案

下載 U-SQL 指令碼的輸出。

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


將要作為輸入的檔案上傳至 U-SQL 指令碼。

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>另請參閱
* 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。
* 若要了解 U-SQL，請參閱 [開始使用 Azure Data Lake Analytics U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。

