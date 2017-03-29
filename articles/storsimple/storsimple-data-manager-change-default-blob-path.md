---
title: "變更預設的 Blob 路徑 | Microsoft Docs"
description: "了解如何設定 Azure 函式來重新命名 Blob 檔案路徑 (私人預覽)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>變更預設的 Blob 路徑 (私人預覽)

此文章說明如何設定 Azure 函式來重新命名預設的 Blob 檔案路徑。 

## <a name="prerequisites"></a>必要條件

開始之前，請確定您有︰
* 已在資源群組內的混合式資料資源中正確設定的工作定義。

## <a name="create-an-azure-function"></a>建立 Azure 函式

執行下列步驟來建立 Azure 函式。

#### <a name="to-create-an-azure-function"></a>建立 Azure 函式

1. 移至 [[Azure 入口網站] ](http://portal.azure.com/) 。

2. 按一下左上角的 [+ 新增]。 在 [搜尋] 文字方塊中輸入「函數應用程式」，然後按下 **Enter**。

    ![移至函數應用程式資源](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. 按一下結果中的 [函數應用程式]。

    ![選取函數應用程式資源](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. 開啟 [函數應用程式] 視窗，然後按一下 [建立]。

    ![建立新的函數應用程式](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. 在 [組態] 刀鋒視窗中，輸入所有的輸入，然後按一下 [建立]。

    1. 應用程式名稱
    2. 訂用帳戶
    3. 資源群組
    4. 主控方案 - **使用情況方案**
    5. 位置
    6. 儲存體帳戶 - 使用現有的儲存體帳戶，或建立新的儲存體帳戶。 儲存體帳戶會在內部針對函式使用。

        ![輸入新的函數應用程式組態資料](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. 建立函數應用程式之後，瀏覽至左下方的 [更多服務 >]。 在 [篩選] 文字方塊中輸入「應用程式服務」，然後按一下 [應用程式服務]。

    ![更多服務 >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. 從 [應用程式服務] 清單中按一下 [函數應用程式名稱]。

8. 按一下 [+ 新函式]。 從 [語言] 下拉式清單中選取 [C#]。 從範本清單中選取 [QueueTrigger-CSharp] 選項。 輸入所有的輸入。

   1. 名稱 - 為您的函式提供名稱。
   2. 佇列名稱 - 輸入您的**資料轉換作業定義名稱**。
   3. 儲存體帳戶連接 - 按一下 [新增] 選項。 選取對應資料轉換作業的帳戶。
      
      記下 `Connection name`。 稍後在 Azure 函式中需要此名稱。

   4. 按一下 [建立] 按鈕。

       ![建立新的 C Sharp 函式 >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. 在 [函式] 視窗中，執行 _.csx_ 檔案。 複製並貼上下列程式碼：

    ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }
    
    ```

   1. 使用您的儲存體帳戶連接，取代行 12 中的實際 **STORAGE_CONNECTIONNAME** (請參閱 8c)。
   2. 按一下左上方的 [儲存] 按鈕。

       ![儲存函式 >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  按一下右側的 [檢視檔案]。

    ![檢視檔案](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. 按一下 [+ 新增]。 輸入 **project.json** 並按 **Enter**。
   2. 複製下列程式碼並在 **project.json** 檔案中貼上。

        ```
            {
            "frameworks": {
                "net46":{
                "dependencies": {
                    "windowsazure.storage": "8.1.1"
                }
                }
            }
            }
        
        ```

   2. 按一下 [儲存] 。

您已建立 Azure 函式。 每一次資料轉換作業產生新的 Blob，都會觸發此函式。

## <a name="next-steps"></a>後續步驟

[使用 StorSimple Data Manager UI 來轉換資料](storsimple-data-manager-ui.md)。

