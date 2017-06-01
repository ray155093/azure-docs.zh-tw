---
title: "運用 Azure 命令列介面開始使用 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何透過 Azure 命令列介面建立 Data Lake Analytics 帳戶、使用 U-SQL 建立 Data Lake Analytics 作業，以及提交作業。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eeaa5641a4bcea0e8b46e85e40ff5b92113446c1
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>教學課程：運用 Azure 命令列介面 (CLI) 開始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何透過 Azure CLI 建立 Azure Data Lake Analytics 帳戶、在 [U-SQL](data-lake-analytics-u-sql-get-started.md)中定義 Data Lake Analytics 工作，以及將工作提交至 Data Lake Analytics 帳戶。 如需有關 Data Lake Analytics 的詳細資訊，請參閱 [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)。

在本教學課程中，您將會開發一個作業以讀取定位鍵分隔值 (TSV) 檔案，並將該檔案轉換為逗點分隔值 (CSV) 檔案。 若要使用其他支援的工具進行同一個教學課程，請按一下此區段最上方的索引標籤。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure CLI**。 請參閱 [安裝和設定 Azure CLI](../cli-install-nodejs.md)。
  * 下載並安裝 **Azure CLI 工具** [發行前版本](https://github.com/MicrosoftBigData/AzureDataLake/releases) ，才能完成這個示範。
* 使用下列命令進行**驗證**：

        azure login
    如需使用公司或學校帳戶驗證的詳細資訊，請參閱 [從 Azure CLI 連線至 Azure 訂用帳戶](../xplat-cli-connect.md)。
* 使用下列命令來**切換至 Azure 資源管理員模式**：

        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶
您必須擁有 Data Lake Analytics 帳戶，才能執行工作。 若要建立 Data Lake Analytics 帳戶，您必須指定下列項目：

* **Azure 資源群組**：Data Lake Analytics 帳戶必須建立在 Azure 資源群組內。 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 可讓您將應用程式中的資源做為群組使用。 您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。  

    若要列舉您訂用帳戶中的資源群組：

        azure group list

    若要建立新的資源群組：

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Data Lake Analytics 帳戶名稱**
* **位置**：其中一個支援 Data Lake Analytics 的 Azure 資料中心。
* **預設 Data Lake A帳戶**：每個 Data Lake Analytics 帳戶都有一個預設的 Data Lake 帳戶。

    若要列出現有的 Data Lake 帳戶：

        azure datalake store account list

    若要建立新的 Data Lake 帳戶：

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

  > [!NOTE]
  > Data Lake 帳戶名稱只能包含小寫字母和數字。
  >
  >

**建立 Data Lake Analytics 帳戶**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Data Lake Analytics 顯示帳戶](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> Data Lake Analytics 帳戶名稱只能包含小寫字母和數字。
>
>

## <a name="upload-data-to-data-lake-store"></a>將資料上傳至 Data Lake Store
在本教學課程中，您將會處理一些搜尋記錄檔。  搜尋記錄檔可以儲存在 Data Lake Store 或 Azure Blob 儲存體中。

Azure 入口網站會提供使用者介面，可將範例資料檔案複製到預設的 Data Lake 帳戶，其中包括搜尋記錄檔案。 若要將資料上傳至預設 Data Lake Store 帳戶，請參閱 [準備來源資料](data-lake-analytics-get-started-portal.md) 。

若要使用 CLI 上傳檔案，請使用下列命令：

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics 也可存取 Azure Blob 儲存體。  若要將資料上傳至 Azure Blob 儲存體，請參閱 [使用 Azure CLI 搭配 Azure 儲存體](../storage/storage-azure-cli.md)。

## <a name="submit-data-lake-analytics-jobs"></a>提交 Data Lake Analytics 工作
Data Lake Analytics 工作是以 U-SQL 語言撰寫。 若要深入了解 U-SQL，請參閱[開始使用 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**建立 Data Lake Analytics 工作指令碼**

* 使用下列 U-SQL 指令碼建立文字檔，並將該檔案儲存到您的工作站：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    此 U-SQL 指令碼會使用 **Extractors.Tsv()** 讀取來源資料檔案，然後使用 **Outputters.Csv()** 建立 csv 檔案。

    除非您將來源檔案複製到其他位置，否則請勿修改這兩個路徑。  Data Lake Analytics 將建立輸出資料夾 (若尚未建立)。

    使用儲存在預設 Data Lake 帳戶中檔案的相對路徑，是比較容易的方法。 您也可以使用絕對路徑。  例如

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    您必須使用絕對路徑存取連結儲存體帳戶中的檔案。  儲存在連結 Azure 儲存體帳戶中之檔案的語法是：

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > 目前不支援具有公用 Blob 或公用容器存取權限的 Azure Blob 容器。      
  >
  >

**提交工作**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


下列命令可用來列出工作、取得工作詳細資料和取消工作：

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

工作完成之後，您可以使用下列 Cmdlet 列出該檔案，並下載檔案：

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>另請參閱
* 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。
* 若要了解更複雜的查詢，請參閱 [使用 Azure Data Lake Analytics 來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
* 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，請參閱 [開始使用 Azure Data Lake Analytics U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 若要取得 Data Lake Analytics 概觀，請參閱 [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)。

