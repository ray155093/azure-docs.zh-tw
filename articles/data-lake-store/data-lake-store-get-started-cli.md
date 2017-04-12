---
title: "使用命令列介面來開始使用 Azure Data Lake Store | Microsoft Docs"
description: "使用 Azure 跨平台命令列建立 Data Lake Store 帳戶並執行基本作業"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f7748dba30c6e0332c166feda25f4aaa93c06efa
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>使用 Azure 命令列開始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 Azure 命令列介面建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關 Data Lake Store 的詳細資訊，請參閱 [Data Lake Store 概觀](data-lake-store-overview.md)。

Azure CLI 會在 Node.js 中實作。 此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。 Azure CLI 為開放原始碼。 原始程式碼會在 GitHub 中進行管理 (<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>)。 本文只涵蓋使用 Azure CLI 搭配 Data Lake Store。 如需如何使用 Azure CLI 的一般指引，請參閱[如何使用 Azure CLI][azure-command-line-tools]。


> [!NOTE]
> 若要上傳和下載大量資料 (大型檔案、大量檔案或兩者)，建議您使用 [Python SDK](data-lake-store-get-started-python.md)、[.NET SDK](data-lake-store-get-started-net-sdk.md) 或 [Azure PowerShell](data-lake-store-get-started-powershell.md)。 這些選項擁有較佳的效能，因為它們會使用多個執行緒平行處理資料移動。
> 
>

## <a name="prerequisites"></a>必要條件
開始閱讀本文之前，您必須符合下列必要條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure CLI** - 請參閱 [安裝及設定 Azure CLI](../cli-install-nodejs.md) 以取得安裝和設定資訊。 在安裝 CLI 之後，請務必重新啟動您的電腦。

## <a name="authentication"></a>驗證

本文使用簡單的驗證方法搭配 Data Lake Store (您以使用者身分登入其中)。 Data Lake Store 帳戶和檔案系統的存取層級則由已登入使用者的存取層級所控管。 不過，還有其他方法可向 Data Lake Store 進行驗證：**使用者驗證**或**服務對服務驗證**。 如需如何驗證的指示和詳細資訊，請參閱 [使用 Azure Active Directory 向 Data Lake Store 進行驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="login-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶

1. 依照[從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)中記載的步驟，使用 `azure login` 方法連接到您的訂用帳戶。

2. 使用 `azure account list` 命令，列出與您的 Azure 帳戶相關聯的訂用帳戶。
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    在上述輸出中，目前已啟用 **Azure-sub-1**，而其他訂用帳戶是 **Azure-sub-2**。 
3. 選取您想要使用的訂用帳戶。 如果您想要使用 Azure-sub-2 訂用帳戶，請使用 `azure account set`。
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>建立 Azure Data Lake Store 帳戶
開啟命令提示字元、殼層或終端機工作階段並執行下列命令。

1. 使用下列命令來切換至 Azure Resource Manager 模式︰
   
        azure config mode arm
2. 建立新的資源群組。 在下列命令中，提供您想要使用的參數值。
   
        azure group create <resourceGroup> <location>
   
    如果位置名稱包含空格，請將它放在引號中。 例如 "East US 2"。
3. 建立 Data Lake Store 帳戶。
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>在您的 Data Lake Store 中建立資料夾
您可以在您的 Azure Data Lake Store 帳戶下建立資料夾，用於管理與存放資料。 使用下列命令在 Data Lake Store 的根目錄建立名為 "mynewfolder" 的資料夾。

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

例如：

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>將資料上傳至您的 Data Lake Store
您可以在根層級直接將資料上傳至 Data Lake Store，或上傳至您在帳戶內建立的資料夾。 下列程式碼片段示範如何將一些範例資料上傳至您在上一節中建立的資料夾 (**mynewfolder**)。

如果您要尋找一些可上傳的範例資料，您可以從 **Azure Data Lake Git 儲存機制** 取得 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)資料夾。 下載檔案並將它儲存在電腦的本機目錄上，例如 C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

例如：

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>列出 Data Lake Store 中的檔案
使用下列命令列出 Data Lake Store 中的檔案。

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

例如：

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

此命令的輸出應類似這樣：

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>重新命名、下載與刪除 Data Lake Store 中的資料
* **若要重新命名檔案**，請使用下列命令：
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    例如：
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **若要下載檔案**，請使用下列命令。 請確定您指定的目的地路徑已存在。
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    例如：
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **若要刪除檔案**，請使用下列命令：
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    例如：
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    出現提示時，請輸入 **Y** 刪除項目。

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>檢視 Data Lake Store 中資料夾的存取控制清單
使用下列命令來檢視 Data Lake Store 資料夾中的 ACL。 在目前版本中，ACL 可以只在 Data Lake Store 的根目錄上設定。 因此，下面的路徑參數一律為根目錄 (/)。

    azure datalake store permissions show <dataLakeStoreName> <path>

例如：

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>刪除 Data Lake Store 帳戶
使用下列命令刪除 Data Lake Store 帳戶。

    azure datalake store account delete <dataLakeStoreAccountName>

例如：

    azure datalake store account delete mynewdatalakestore

出現提示時，請輸入 **Y** 刪除帳戶。

## <a name="next-steps"></a>後續步驟
* [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../cli-install-nodejs.md

