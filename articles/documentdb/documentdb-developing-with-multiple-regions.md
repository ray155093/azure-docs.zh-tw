---
title: "在 DocumentDB 中使用多個區域進行開發 | Microsoft Docs"
description: "了解如何從 Azure DocumentDB (可完全管理的 NoSQL 資料庫服務) 存取多個區域中的資料。"
services: documentdb
documentationcenter: 
author: kiratp
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: kipandya
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: d7ed490157eff98e31c824daf1f9af98ae1afeaa


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>使用多個區域 DocumentDB 帳戶進行開發
> [!NOTE]
> 對於任何新建的 DocumentDB 帳戶，通常可以使用 DocumentDB 資料庫的全球發佈，而且會自動啟用。 我們正致力於啟用所有現有帳戶的全球發佈，但如果您想在這過渡時期啟用您帳戶的全球發佈，請 [連絡客戶支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ，我們將立即為您啟用。
>
>

為了充分運用 [全球發佈](documentdb-distribute-data-globally.md)，用戶端應用程式可以指定已排序的區域喜好設定清單，以用來執行文件作業。 這可透過設定連接原則來完成。 根據 Azure DocumentDB 帳戶組態、目前區域可用性和指定的喜好設定清單，SDK 將會選擇最適合的端點來執行寫入和讀取作業。

在使用 DocumentDB 用戶端 SDK 初始化連接時，即會指定這份喜好設定清單。 SDK 會接受選擇性參數 "PreferredLocations"，也就是已排序的 Azure 區域清單。

SDK 會自動將所有寫入傳送至目前的寫入區域。

所有讀取都將傳送至 PreferredLocations 清單中的第一個可用區域。 如果要求失敗，用戶端將無法往下到清單中的下一個區域，依此類推。

用戶端 SDK 只會嘗試讀取 PreferredLocations 中所指定的區域。 因此，比方說，如果資料庫帳戶可供三個區域使用，但用戶端只針對 PreferredLocations 指定其中兩個非寫入區域，則將不會在該寫入區域以外的地方提供讀取服務，即使發生容錯移轉也一樣。

應用程式可以藉由檢查兩個屬性 (WriteEndpoint 和 ReadEndpoint，適用於 SDK 1.8 版和以上版本) 來確認 SDK 目前所選擇的寫入端點和讀取端點。

如果未設定 PreferredLocations 屬性，將會從目前的寫入區域為所有要求提供服務。

## <a name="net-sdk"></a>.NET SDK
您不需變更任何程式碼即可使用 SDK。 在此情況下，SDK 會自動將讀取和寫入導向至目前寫入區域。

在 .NET SDK 的 1.8 版和更新版本中，適用於 DocumentClient 建構函式的 ConnectionPolicy 參數會有一個名為 Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations 的屬性。 這個屬性的類型是 Collection `<string>` ，而且應包含區域名稱的清單。 字串值已按照 [Azure 區域][regions]頁面的 [區域名稱] 欄而格式化，而且在第一個字元之前和最後一個字元之後沒有空格。

目前的寫入和讀取端點分別適用於 DocumentClient.WriteEndpoint 和 DocumentClient.ReadEndpoint。

> [!NOTE]
> 不應將端點的 URI 視為長時間執行的常數。 服務可能會隨時更新這些項目。 SDK 會自動處理此變更。
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
    
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS、JavaScript 和 Python SDK
您不需變更任何程式碼即可使用 SDK。 在此情況下，SDK 會自動將讀取和寫入導向至目前寫入區域。

在每個 SDK 的 1.8 版和更新版本中，適用於 DocumentClient 建構函式的 ConnectionPolicy 參數會有一個名為 DocumentClient.ConnectionPolicy.PreferredLocations 的新屬性。 這個參數是取得區域名稱清單的字串陣列。 名稱已按照 [Azure 區域][regions]頁面的 [區域名稱] 欄而格式化。 您也可以在方便的物件 AzureDocuments.Regions 中使用預先定義的常數

目前的寫入和讀取端點分別適用於 DocumentClient.getWriteEndpoint 和 DocumentClient.getReadEndpoint。

> [!NOTE]
> 不應將端點的 URI 視為長時間執行的常數。 服務可能會隨時更新這些項目。 SDK 將會自動處理此變更。
>
>

以下是 NodeJS/Javascript 程式碼範例。 Python 和 Java 都將遵循相同模式。

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST
一旦資料庫帳戶可供多個區域使用之後，用戶端就可藉由在下列 URI 上執行 GET 要求來查詢其可用性。

    https://{databaseaccount}.documents.azure.com/

服務將針對複本傳回區域清單及其對應的 DocumentDB 端點 URI。 回應中將會指出目前的寫入區域。 用戶端接著可針對所有未來的 REST API 要求選取適當的端點，如下所示。

範例回應

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* 所有的 PUT、POST 和 DELETE 要求都必須移至指定的寫入 URI
* 所有的 GET 和其他唯讀要求 (例如查詢) 可能會移至用戶端選擇的任何端點

將要求寫入至唯讀區域將會失敗，並產生 HTTP 錯誤碼 403 (「禁止」)。

如果寫入區域在用戶端初始探索階段之後變更，則寫入至上一個寫入區域的後續作業將會失敗，並產生 HTTP 錯誤碼 403 (「禁止」)。 用戶端接著應再次 GET 區域清單，以取得更新的寫入區域。

## <a name="next-steps"></a>後續步驟
深入了解下列文章中使用 DocumentDB 全球發佈資料的相關資訊：

* [Distribute data globally with DocumentDB (使用 DocumentDB 全球發佈資料)](documentdb-distribute-data-globally.md)
* [一致性層級](documentdb-consistency-levels.md)
* [使用 Azure 入口網站新增區域](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/



<!--HONumber=Jan17_HO2-->


