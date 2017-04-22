---
title: "DocumentDB 全球資料庫複寫 | Microsoft Docs"
description: "了解如何透過 Azure 入口網站管理 DocumentDB 帳戶的全球複寫。"
services: documentdb
keywords: "全域資料庫, 複寫"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cc393967fec1a98a4dd596a156c7a12e88959b04
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-perform-global-database-replication-using-the-azure-portal"></a>如何使用 Azure 入口網站執行全球資料庫複寫

了解如何在 Azure DocumentDB 和 API for MongoDB 中使用 Azure 入口網站複寫多個區域中的資料，以確保資料的全球可用性。

有關 DocumentDB 中全球資料庫複寫的運作方式，詳細資訊請參閱 [使用 DocumentDB 全球發佈資料](documentdb-distribute-data-globally.md)。 如需以程式設計方式執行全球資料庫複寫的相關資訊，請參閱 [使用多個區域 DocumentDB 帳戶進行開發](documentdb-developing-with-multiple-regions.md)。

## <a id="addregion"></a>新增全球資料庫區域
在大部分 [Azure 區域][azureregions]中可使用 DocumentDB。 選取資料庫帳戶的預設一致性層級之後，您可以關聯一或多個區域 (取決於您對於預設一致性層級和全球發佈需求的選擇)。

1. 在 [Azure 入口網站](https://portal.azure.com/)的左側工具列中，按一下 [NoSQL (DocumentDB)]。
2. 在 [NoSQL (DocumentDB)] 刀鋒視窗中，選取要修改的資料庫帳戶。
3. 在帳戶刀鋒視窗中，從功能表中按一下 [全域複寫資料]。
4. 在 [全域複寫資料] 刀鋒視窗中，選取要新增或移除的區域，然後按一下 [儲存]。 新增區域需要費用，如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)或[使用 DocumentDB 全球發佈資料](documentdb-distribute-data-globally.md)一文。
   
    ![按一下地圖中的區域以新增或移除它們][1]
    
一旦您加入第二個區域，就會在入口網站中的 [在本機複寫資料] 刀鋒視窗上啟用 [手動容錯移轉] 選項。 您可以使用此選項來測試容錯移轉程序。 一旦您加入第三個區域，會在相同的刀鋒視窗上啟用 [容錯移轉優先順序] 選項，因此您可以變更讀取的容錯移轉順序。  

### <a name="selecting-global-database-regions"></a>選取全球資料庫區域
設定兩個以上的區域時，建議根據[業務持續性和災害復原 (BCDR)：Azure 配對的區域][bcdr]文章中所述的區域配對來選取區域。

具體來說，在設定多個區域時，請務必從每個配對的區域資料行中選取相同數目的區域 (+/-1 代表奇數/偶數)。 例如，如果您想要部署到 4 個美國地區，可從左側資料行選取 2 個美國地區，然後從右側選取 2 個。 因此，以下是一個適當的組合︰美國西部、美國東部、美國中北部和美國中南部。

如果只針對災害復原案例設定 2 個區域，請務必遵循本指南。 針對 2 個以上的區域，遵循本指南是一個很好的做法，但只要部分選取的區域會遵守此配對，就不是那麼重要。

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

### <a name="verifying-your-regional-setup-in-api-for-mongodb"></a>在 API for MongoDB 中驗證您的區域設定
若要在 API for MongoDB 內仔細檢查檢查您的全球組態，最簡單的方法就是從 Mongo 殼層執行 *isMaster()* 命令。

從您的 Mongo 殼層︰

   ```
      db.isMaster()
   ```
   
範例結果︰

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10250",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10250",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10250",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
      }
   ```

## <a id="next"></a>接續步驟
如欲了解如何管理您的全球複寫帳戶的一致性，請閱讀 [DocumentDB 中的一致性層級](documentdb-consistency-levels.md)。

有關 DocumentDB 中全球資料庫複寫的運作方式，詳細資訊請參閱 [使用 DocumentDB 全球發佈資料](documentdb-distribute-data-globally.md)。 如需以程式設計方式在多個區域複寫資料的相關資訊，請參閱 [使用多個區域 DocumentDB 帳戶進行開發](documentdb-developing-with-multiple-regions.md)。

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

