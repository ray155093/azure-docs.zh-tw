---
title: "開始使用 Data Lake Store | Microsoft Docs"
description: "使用 Azure PowerShell 建立資料湖存放區帳戶，並執行基本作業"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a86fd04a7ec0cffabe42d30132b97777c752bbde
ms.openlocfilehash: e37b698436c067faa20b0e589078927d5955934a


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>使用 Azure PowerShell 開始使用 Azure 資料湖分析存放區
> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 Azure PowerShell 建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關 Data Lake Store 的詳細資訊，請參閱 [Data Lake Store 概觀](data-lake-store-overview.md)。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更新版本**。 請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="authentication"></a>驗證
本文搭配使用較簡單的驗證方法與 Data Lake Store，系統會提示您輸入 Azure 帳號認證。 Data Lake Store 帳戶和檔案系統的存取層級則由已登入使用者的存取層級所控管。 不過，還有其他方法可向 Data Lake Store 進行驗證：**使用者驗證**或**服務對服務驗證**。 如需如何驗證的指示和詳細資訊，請參閱 [使用 Azure Active Directory 向 Data Lake Store 進行驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="create-an-azure-data-lake-store-account"></a>建立 Azure 資料湖存放區帳戶
1. 從您的桌面上開啟新的 Windows PowerShell 視窗，輸入下列程式碼片段登入 Azure 帳戶、設定訂用帳戶，然後註冊 Data Lake Store 提供者。 系統提示您登入時，請使用其中一個訂用帳戶管理員/擁有者身分登入：

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure 資料湖存放區帳戶與 Azure 資源群組相關聯。 從建立 Azure 資源群組開始。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![建立 Azure 資源群組](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "建立 Azure 資源群組")
3. 建立 Azure 資料湖存放區帳戶。 您指定的名稱必須只包含小寫字母和數字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![建立 Azure Data Lake Store 帳戶](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "建立 Azure Data Lake Store 帳戶")
4. 確認已成功建立帳戶。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    輸出應為 **True**。

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>在您的 Azure 資料湖存放區中建立目錄結構
您可以在您的 Azure 資料湖存放區帳戶下建立用於管理與儲存資料的目錄。

1. 指定根目錄。

        $myrootdir = "/"
2. 在指定的根目錄下建立名為 **mynewdirectory** 的新目錄。

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 確認已成功建立新目錄。

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    應該會顯示類似下面的輸出畫面：

    ![確認目錄](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "確認目錄")

## <a name="upload-data-to-your-azure-data-lake-store"></a>將資料上傳至 Azure 資料湖存放區
您可以在根層級直接將資料上傳至資料湖存放區，或上傳至您在帳戶內建立的目錄。 下列程式碼範例說明如何將一些範例資料上傳至您在上一節中建立的目錄 (**mynewdirectory**)。

如果您要尋找一些可上傳的範例資料，您可以從 **Azure 資料湖 Git 儲存機制** 取得 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)資料夾。 下載檔案並將它儲存在電腦的本機目錄上，例如 C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>重新命名、下載與刪除資料湖存放區中的資料
若要重新命名檔案，請使用下列命令：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

若要下載檔案，請使用下列命令：

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

若要刪除檔案，請使用下列命令：

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

出現提示時，請輸入 **Y** 刪除項目。 如果您要刪除多個檔案，可以提供所有的路徑並以逗號分隔。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>刪除 Azure 資料湖存放區帳戶
使用下列命令刪除資料湖存放區帳戶。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出現提示時，請輸入 **Y** 刪除帳戶。

## <a name="performance-guidance-while-using-powershell"></a>使用 PowerShell 時的效能指引

以下是可以微調的最重要設定，以在使用 PowerShell 搭配 Data Lake Store 運作時獲得最佳效能︰

| 屬性            | 預設值 | 說明 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | 這個參數可讓您選擇可用於上傳或下載每個檔案的平行執行緒數目。 此數字代表每個檔案可以配置的執行緒數目上限，但視您的案例而定，您可能會收到比較少的執行緒 (例如︰如果您要上傳 1 KB 檔案，即使您要求 20 個執行緒，但您將取得一個執行緒)。  |
| ConcurrentFileCount | 10      | 這個參數特別用於上傳或下載資料夾。 這個參數會決定可以上傳或下載的並行檔案數目。 此數字代表可以一次上傳或下載的並行檔案數目上限，但視您的案例而定，您可能會收到比較少的並行檔案 (例如︰如果您要上傳兩個檔案，即使您要求 15 個檔案，但您將取得兩個並行檔案)。 |

**範例**

此命令會使用每個檔案 20 個執行緒和 100 個並行檔案，將檔案從 Azure Data Lake Store 下載至使用者的本機磁碟機。

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>如何判斷要針對這些參數設定的值？

以下是一些您可以使用的指引。

* **步驟 1︰決定總執行緒計數** - 您應該從計算要使用的總執行緒計數著手。 一般來說，您應該針對每個實體核心使用 6 個執行緒。

        Total thread count = total physical cores * 6

    **範例**

    假設您正從有 16 個核心的 D14 VM 執行 PowerShell 命令

        Total thread count = 16 cores * 6 = 96 threads


* **步驟 2︰計算 PerFileThreadCount** - 我們會根據檔案的大小計算 PerFileThreadCount。 對於小於 2.5 GB 的檔案，不需要變更此參數，因為預設值為 10 就已足夠。 對於大於 2.5 GB 的檔案，您應該使用 10 個執行緒做為第一個 2.5 GB 的基底，並且為每增加額外 256 MB 的檔案大小新增 1 個執行緒。 如果您要複製包含各種檔案大小的資料夾，請考慮將其分組為相似的檔案大小。 檔案大小不相近可能會導致非最佳的效能。 如果不可能分組為相似的檔案大小，您應該根據最大檔案大小設定 PerFileThreadCount。

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **範例**

    假設您有 100 個 1GB 到 10GB 的檔案，我們使用 10GB 做為等式的最大檔案大小，該等式會如下所示。

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **步驟 3︰計算 ConcurrentFilecount** - 使用總執行緒計數和 PerFileThreadCount 來計算以下列等式為基礎的 ConcurrentFileCount。

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **範例**

    以我們使用的範例值為基礎

        96 = 40 * ConcurrentFileCount

    因此，**ConcurrentFileCount** 是 **2.4**, ，我們可以四捨五入為 **2**。

### <a name="further-tuning"></a>進一步微調

您可能需要進一步微調，因為有各種檔案大小要處理。 如果所有或大部分檔案都比較大且比較接近 10GB 的範圍，就很適合上述的計算。 相反地，如果有許多不同的檔案大小且很多檔案比較小，您可以減少 PerFileThreadCount。 藉由減少 PerFileThreadCount，我們即可增加 ConcurrentFileCount。 所以，如果假設 5GB 範圍中的大部分檔案比較小，我們可以重新進行計算︰

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

因此，**ConcurrentFileCount** 現在會是 96/20，也就是 4.8，可四捨五入為 **4**。

您可以根據檔案大小的分佈情形，將 **PerFileThreadCount** 變大和變小，繼續微調這些設定。

### <a name="limitation"></a>限制

* **檔案數目小於 ConcurrentFileCount**︰如果您要上傳的檔案數目小於您計算的 **ConcurrentFileCount**，則應該減少 **ConcurrentFileCount** 使其等於檔案數目。 您可以使用任何剩餘的執行緒來增加 **PerFileThreadCount**。

* **執行緒太多**︰如果您增加太多執行緒計數，但未增加您的叢集大小，您會有效能降低的風險。 在 CPU 上進行環境切換時，可能會有爭用問題。

* **並行處理量不足**︰如果並行處理量不足，您的叢集可能太小。 您可以增加叢集中的節點數目，以提供更多的並行處理量。

* **節流錯誤**︰如果並行處理量太高，您可能會看到節流錯誤。 如果您看到節流錯誤，則應該減少並行處理量或與我們連絡。

## <a name="next-steps"></a>後續步驟
* [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Dec16_HO2-->


