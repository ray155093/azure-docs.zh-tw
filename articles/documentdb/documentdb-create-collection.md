---
title: "建立 DocumentDB 資料庫和集合 | Microsoft Docs"
description: "了解如何使用 Azure DocumentDB (一個雲端型文件資料庫) 的線上服務入口網站來建立 NoSQL 資料庫和 JSON 文件集合。 立即取得免費試用版。"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: f8e0b05301b83223a7cb15d55be7001f3299213f


---
# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>如何使用 Azure 入口網站建立 DocumentDB 集合和資料庫
若要使用 Microsoft Azure DocumentDB，您必須擁有 [DocumentDB 帳戶](documentdb-create-account.md)、資料庫、集合和文件。 本主題說明如何在 Azure 入口網站中建立 DocumentDB 集合。

不確定集合是什麼嗎？ 請參閱＜ [什麼是 DocumentDB 集合？](#what-is-a-documentdb-collection)

1. 在 [Azure 入口網站](https://portal.azure.com/)的動態工具列中，按一下 [DocumentDB (NoSQL)]，然後在 [DocumentDB (NoSQL)] 刀鋒視窗中，選取要新增集合的帳戶。 如果您沒有任何列出的帳戶，您需要 [建立 DocumentDB 帳戶](documentdb-create-account.md)。

   ![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   如果在動態工具列中看不到 [DocumentDB (NoSQL)]，請按一下 [更多服務]，然後按一下 [DocumentDB (NoSQL)]。 如果您沒有任何列出的帳戶，您需要 [建立 DocumentDB 帳戶](documentdb-create-account.md)。
2. 在所選帳戶的 [DocumentDB 帳戶] 刀鋒視窗中，按一下 [加入集合]。

    ![螢幕擷取畫面，其中在 [資料庫] 透鏡裡反白顯示動態工具列的 [DocumentDB 帳戶]、[DocumentDB 帳戶] 刀鋒視窗中的帳戶和 [DocumentDB 帳戶] 刀鋒視窗中的資料庫](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. 在 [加入集合] 刀鋒視窗的 [識別碼] 方塊中，輸入新集合的識別碼。 集合名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。 驗證名稱時，[識別碼] 方塊中會出現綠色的核取記號。

    ![螢幕擷取畫面，其中強調 [資料庫] 刀鋒視窗上的 [新增集合] 按鈕、[新增集合] 刀鋒視窗上的設定，以及 [確定] 按鈕 - 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. [定價層] 預設會設定為 [標準]，讓您可以自訂集合的輸送量和儲存體。 如需定價層的詳細資訊，請參閱 [DocumentDB 中的效能等級](documentdb-performance-levels.md)。  
5. 選取集合的 [資料分割模式]，亦即 [單一資料分割] 或 [已分割]。

    **單一資料分割** 保留的儲存體容量為 10 GB，且輸送量層級可以從每秒 400 - 10,000 個要求單位 (RU/s)。 一個 RU 會對應至讀取 1 KB 文件的輸送量。 如需要求單位的詳細資訊，請參閱 [要求單位](documentdb-request-units.md)。

    可調整 **已資料分割的集合** 以處理多個資料分割上不限數目的儲存體，且輸送量層級可以從 10,100 RU/s 起跳。 在入口網站中，您可以保留的最大儲存體是 250 GB，可以保留的最大輸送量為 250,000 RU/s。 若要增加任一配額，請提出要求，如 [要求增加 DocumentDB 帳戶配額](documentdb-increase-limits.md)中所述。 如需分割資料集合的詳細資訊，請參閱 [單一資料分割和已資料分割的集合](documentdb-partition-data.md#single-partition-and-partitioned-collections)。

    新的單一資料分割集合預設會設定為 1000 RU/秒，儲存體容量為 10 GB。 針對已分割的集合，集合輸送量是設定為 10100 RU/秒，儲存體容量為 250 GB。 您可以在集合建立之後，變更集合的輸送量和儲存體。
6. 如果您要建立已資料分割的集合，請選取集合的 [資料分割索引鍵]  。 選取正確的資料分割索引鍵對於建立高效能集合來說很重要。 如需選取資料分割索引鍵的詳細資訊，請參閱 [資料分割設計](documentdb-partition-data.md#designing-for-partitioning)。
7. 在 [資料庫] 刀鋒視窗中，建立新的資料庫或使用現有的資料庫。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。 若要驗證名稱，請在文字方塊外按一下。 名稱通過驗證時，方塊中會出現綠色核取記號。
8. 按一下螢幕底部的 [ **確定** ]，以建立新的集合。
9. 新的集合此時便會出現在 [概觀] 刀鋒視窗上的 [集合] 功能濾鏡中。

    ![[資料庫] 刀鋒視窗中新集合的螢幕擷取畫面- 適用於 DocumentDB 的 Azure 入口網站 - NoSQL JSON 資料庫的雲端式資料庫建立者](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **選擇性︰**若要在入口網站中修改集合的輸送量，請按一下 [資源] 功能表上的 [級別]。

    ![資源功能表的螢幕擷取畫面，其中已選取 [級別]](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>什麼是 DocumentDB 集合？
集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。 集合是計費實體，其 [成本](documentdb-performance-levels.md) 是由集合所佈建的輸送量所決定。 集合可以跨越一或多個資料分割/伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

集合會由 DocumentDB 自動分割成一或多個實體伺服器。 當您建立集合時，您可以指定以每秒的要求單位和分割索引鍵屬性佈建的輸送量。 DocumentDB 將會使用此屬性的值將文件散佈到分割並路由要求 (例如查詢)。 分割索引鍵值也做為預存程序和觸發程序的交易界限。 每個集合都有該集合特定的保留輸送量，且不會與相同帳戶中的其他集合共用。 因此，您可以在儲存體和輸送量方面相應放大您的應用程式。

集合與關聯式資料庫中的資料表不同。 集合不會強制使用結構描述；事實上，DocumentDB 不會強制使用任何結構描述，它是無結構描述的資料庫。 因此，您可以在相同集合中儲存具有各種不同結構描述之不同類型的文件。 您可以選擇使用集合來儲存單一類型的物件，正如同您會對資料表所做的一樣。 最佳模型僅取決於資料一起出現在查詢和交易中的方式。

## <a name="other-ways-to-create-a-documentdb-collection"></a>建立 DocumentDB 集合的其他方法
集合不一定要使用入口網站來建立，您也可以使用 [DocumentDB SDK](documentdb-sdk-dotnet.md) 和 REST API 來建立集合。

* 如需 C# 程式碼範例，請參閱 [C# 集合範例](documentdb-dotnet-samples.md#collection-examples)。
* 如需 Node.js 程式碼範例，請參閱 [Node.js 集合範例](documentdb-nodejs-samples.md#collection-examples)。
* 如需 Python 程式碼範例，請參閱 [Python 集合範例](documentdb-python-samples.md#collection-examples)。
* 如需 REST API 範例，請參閱 [建立集合](https://msdn.microsoft.com/library/azure/mt489078.aspx)。

## <a name="troubleshooting"></a>疑難排解
如果 Azure 入口網站的 [新增集合  ] 遭到停用，這代表您的帳戶目前已遭停用，這種情況通常會在當月的所有權益信用額度都用完時發生。    

## <a name="next-steps"></a>後續步驟
既然您已有了集合，下一個步驟是將文件加入或滙入集合。 談到將文件加入集合時，您會有幾個選擇：

* 您可以使用入口網站中的文件總管來 [新增文件](documentdb-view-json-document-explorer.md) 。
* 您可以使用 DocumentDB 資料移轉工具來 [匯入文件和資料](documentdb-import-data.md) ，此工具可讓您匯入 JSON 和 CSV 檔案，以及來自 SQL Server、MongoDB、Azure 資料表儲存體及其他 DocumentDB 集合的資料。
* 或者，您可以利用其中一個 [DocumentDB SDK](documentdb-sdk-dotnet.md)來新增文件。 DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。 如需示範如何使用 DocumentDB .NET SDK 處理文件的 C# 程式碼範例，請參閱 [C# 文件範例](documentdb-dotnet-samples.md#document-examples)。 如需示範如何使用 DocumentDB Node.js SDK 處理文件的 Node.js 程式碼範例，請參閱 [Node.js 文件範例](documentdb-nodejs-samples.md#document-examples)。

在集合中有了文件之後，您便可以藉由使用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](documentdb-sdk-dotnet.md)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-sql-queries)。 



<!--HONumber=Nov16_HO3-->


