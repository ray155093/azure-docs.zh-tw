---
title: "將來自串流分析的資料串流處理至 Data Lake Store | Microsoft Docs"
description: "使用 Azure 串流分析將資料串流處理至 Azure Data Lake Store"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/07/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 2864fbf1fc4f070cb4d88d1bb3efbaaf408c68ce


---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>使用 Azure 串流分析將來自 Azure 儲存體 Blob 的資料串流處理至 Data Lake Store
在這篇文章中，您將了解如何使用 Azure Data Lake Store 做為 Azure 串流分析作業的輸出。 這篇文章示範從 Azure 儲存體 Blob (輸入) 讀取資料以及將資料寫入至 Data Lake Store (輸出) 的簡單案例。

> [!NOTE]
> 目前只有在 [Azure 傳統入口網站](https://manage.windowsazure.com)支援建立及設定串流分析的 Data Lake Store 輸出。 因此，此教學課程的部份內容會使用 Azure 傳統入口網站。
>
>

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **啟用您的 Azure 訂用帳戶** 以使用「Data Lake Store 公開預覽版」。 請參閱 [指示](data-lake-store-get-started-portal.md)。
* **Azure 儲存體帳戶**。 您將使用來自此帳戶的 Blob 容器來輸入串流分析作業的資料。 本教學課程中，假設您建立名為 **datalakestoreasa** 的儲存體帳戶，並在該帳戶中建立名為 **datalakestoreasacontainer** 的容器。 一旦您已建立容器，請將範例資料檔案上傳給容器。 您可以從 [Azure Data Lake Git 儲存機制](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)取得範例資料檔案。 您可以使用各種用戶端，例如： [Azure 儲存體總管](http://storageexplorer.com/)，將資料上傳至 Blob 容器。

  > [!NOTE]
  > 如果您是從 Azure 入口網站建立帳戶，請確定您以 **傳統** 部署模型建立。 這可確保儲存體帳戶可從 Azure 傳統入口網站進行存取，因為這是我們用來建立串流分析作業的方式。 如需關於如何從 Azure 入口網站使用傳統部署建立儲存體帳戶的指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。
  >
  > 或者，您可以從 Azure 傳統入口網站來建立儲存體帳戶。
  >
  >
* **Azure Data Lake Store 帳戶**。 遵循 [使用 Azure 入口網站開始使用 Azure 資料湖存放區](data-lake-store-get-started-portal.md)的指示。  

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業
您可以從建立包含輸入來源和輸出目的地的串流分析作業開始。 本教學課程中，來源是 Azure Blob 容器，而目的地則是 Data Lake Store。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 從畫面左下方，依序按一下 [新增]、[資料服務]、[串流分析] 和 [快速建立]。 提供如下所示的值，然後按一下 [建立串流分析作業] 。

    ![建立串流分析作業](./media/data-lake-store-stream-analytics/create.job.png "Create a Stream Analytics job")

## <a name="create-a-blob-input-for-the-job"></a>建立作業的 Blob 輸入
1. 開啟「串流分析作業」頁面，按一下 [輸入] 索引標籤，然後再按一下 [新增輸入] 來啟動精靈。
2. 在**將輸入新增至您的作業**頁面上，選取 [資料流]，然後按下一步箭頭。

    ![將輸入新增至您的作業](./media/data-lake-store-stream-analytics/create.input.1.png "Add an input to your job")
3. 在**將資料流新增至您的作業**頁面上，選取 [Blob 儲存體]，然後按下一步箭頭。

    ![將資料流新增至作業](./media/data-lake-store-stream-analytics/create.input.2.png "Add a data stream to the job")
4. 在 **「Blob 儲存體設定」** 頁面上，提供您會用來當做輸入資料來源的 Blob 儲存體詳細資料。

    ![提供 Blob 儲存體設定](./media/data-lake-store-stream-analytics/create.input.3.png "Provide the blob storage settings")

   * **輸入一個輸入別名**。 這是提供作業輸入用的唯一名稱。
   * **選取儲存體帳戶**。 請確定儲存體帳戶和串流分析作業位於相同的區域，否則在區域之間移動資料，您會需要支付額外費用。
   * **提供儲存體容器**。 您可以選擇建立新的容器，或選取現有的容器。

     按一下向前箭號。
5. 在**序列化設定**頁面上，將序列化格式設定為 **CSV**、分隔符號設定為 **Tab**，而編碼設定為 **UTF8**，然後按一下核取記號。

    ![提供序列化設定](./media/data-lake-store-stream-analytics/create.input.4.png "Provide the serialization settings")
6. 一旦您完成精靈之後，會將 Blob 輸入新增到 [輸入] 索引標籤之下，而 [診斷] 欄位應該會顯示 [確定]。 您也可以使用下方的 [測試連線]  按鈕，明確地測試輸入的連線。

## <a name="create-a-data-lake-store-output-for-the-job"></a>建立作業的 Data Lake Store 輸出
1. 開啟「串流分析作業」頁面，按一下 [輸出] 索引標籤，然後再按一下 [新增輸出] 來啟動精靈。
2. 在**將輸出新增至您的作業**頁面上，選取 [Data Lake Store]，然後按下一步箭頭。

    ![將輸出新增至您的作業](./media/data-lake-store-stream-analytics/create.output.1.png "Add an output to your job")
3. 在**授權連線**頁面上，如果您已建立 Data Lake Store 帳戶，請按一下 [立即授權]。 否則，請按一下 [立即註冊]  來建立新帳戶。 本教學課程中，讓我們假設您已建立 Data Lake Store 帳戶 (如必要條件中所述)。 您將使用登入 Azure 傳統入口網站的認證來取得自動授權。

    ![授權 Data Lake Store](./media/data-lake-store-stream-analytics/create.output.2.png "Authorize Data Lake Store")
4. 在「Data Lake Store」設定  頁面上，輸入如下方螢幕擷取畫面中所示的資訊。

    ![指定 Data Lake Store 設定](./media/data-lake-store-stream-analytics/create.output.3.png "Specify Data Lake Store settings")

   * **輸入一個輸出別名**。 這是提供作業輸出用的唯一名稱。
   * **指定 Data Lake Store 帳戶**。 您應該已經建立此帳戶，如必要條件中所述。
   * **指定路徑前置詞模式**。 您必須指定此項目，才能識別由串流分析作業寫入至 Data Lake Store 的輸出檔案。 因為由作業所寫入的輸出標題為 GUID 格式，因此納入前置詞可協助識別已寫入的輸出。 如果您想要包含日期和時間戳記，做為前置詞的一部分，請確定將 `{date}/{time}` 納入前置詞模式中。 如果您納入此模式，就會啟用 [日期格式] 和 [時間格式] 欄位，而且可以選取希望使用的格式。

     按一下向前箭號。
5. 在**序列化設定**頁面上，將序列化格式設定為 **CSV**、分隔符號設定為 **Tab**，而編碼設定為 **UTF8**，然後按一下核取記號。

    ![指定輸出格式](./media/data-lake-store-stream-analytics/create.output.4.png "Specify the output format")
6. 一旦您完成精靈之後，會將 Data Lake Store 輸出新增到 [輸出] 索引標籤之下，而 [診斷] 欄位應該會顯示 [確定]。 您也可以使用下方的 [測試連接]  按鈕，明確地測試至輸出的連接。

## <a name="run-the-stream-analytics-job"></a>執行串流分析作業
若要執行串流分析作業，您必須從 [查詢] 索引標籤來執行查詢。 本教學課程中，您可以藉由以作業輸入和輸出別名取代預留位置的方式執行範例查詢，如下方螢幕擷取畫面所示。

![執行查詢](./media/data-lake-store-stream-analytics/run.query.png "Run query")

在畫面下方按一下 [儲存]，然後按一下 [啟動]。 從對話方塊中，選取 [自訂時間]，然後選取過去的日期，例如：**2016/1/1**。 按一下核取記號以啟動作業。 啟動作業會需要費時幾分鐘。

![設定作業時間](./media/data-lake-store-stream-analytics/run.query.2.png "Set job time")

作業啟動之後，請按一下 [監視]  索引標籤，查看資料處理方式。

![監視作業](./media/data-lake-store-stream-analytics/run.query.3.png "Monitor job")

最後，您可以使用 [Azure 入口網站](https://portal.azure.com) 來開啟 Data Lake Store 帳戶，並驗證資料是否已成功寫入帳戶。

![驗證輸出](./media/data-lake-store-stream-analytics/run.query.4.png "Verify output")

請注意，在「資料總管」窗格中，輸出會寫入至 Data Lake Store 輸出設定中所指定的資料夾 (`streamanalytics/job/output/{date}/{time}`)。  

## <a name="see-also"></a>另請參閱
* [建立 HDInsight 叢集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


