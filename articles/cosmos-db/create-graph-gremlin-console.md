---
title: "Azure Cosmos DB 教學課程︰在Apache TinkerPops Gremlin 主控台中建立、查詢和周遊 | Microsoft Docs"
description: "Azure Cosmos DB 快速入門，說明如何使用 Azure Cosmos DB 圖形 API建立頂點、邊緣和查詢。"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/19/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: caf3b69b25ccd15322054a0bbf95fc2a5816e00a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/20/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB︰在 Gremlin 主控台中建立、查詢和周遊圖形

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立 Azure Cosmos DB 帳戶、資料庫和圖形 (容器)，然後從 [Apache TinkerPop](http://tinkerpop.apache.org) 使用 [Gremlin 主控台](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console)來處理圖形 API (預覽) 資料。 在本教學課程中，您將建立和查詢頂點和邊緣、更新頂點屬性、查詢頂點、周遊該圖形，以及刪除頂點。

![Apache Gremlin 主控台中的 Azure Cosmos DB](./media/create-graph-gremlin-console/gremlin-console.png)

Gremlin 主控台是以 Groovy/Java 為基礎並且在 Linux、Mac 和 Windows 上執行。 您可以從 [Apache TinkerPop 網站](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip)進行下載。

## <a name="prerequisites"></a>必要條件

您必須擁有 Azure 訂用帳戶，才能針對本快速入門建立 Azure Cosmos DB 帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

您也需要安裝 [Gremlin 主控台](http://tinkerpop.apache.org/)。 使用 3.2.4 版或更高版本。

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>連線到您的應用程式服務
1. 啟動 Gremlin 主控台之前，請建立或修改 apache-tinkerpop-gremlin-console-3.2.4/conf目錄中的 remote-secure.yaml 組態檔。
2. 填入您的 host、port、username、password、connectionPool 和 serializer 組態︰

    設定|建議的值|說明
    ---|---|---
    主機|***.graphs.azure.com|您可以從 Azure 入口網站擷取的圖形服務 URI
    連接埠|443|設為 443
    使用者名稱|您的使用者名稱**|`/dbs/<db>/colls/<coll>` 形式的資源。
    密碼|您的主要金鑰**|Azure Cosmos DB 的主要金鑰
    ConnectionPool|{enableSsl: true}|SSL 的連線集區設定
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|設為這個值

3. 在您的終端機執行 bin/gremlin.bat 或 bin/gremlin.sh 以啟動 [Gremlin 主控台](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/)。
4. 在您的終端機執行 :remote connect tinkerpop.server conf/remote-secure.yaml 以連線到您的應用程式服務。

太棒了！ 現在已完成安裝程式，讓我們開始執行一些主控台命令。

## <a name="create-vertices-and-edges"></a>建立頂點和邊緣

首先我們會新增四個人員頂點 Thomas、Mary Kay、Robin 和 Ben。

輸入 (Thomas)：

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

輸出：

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
輸入 (Mary Kay)：

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

輸出：

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

輸入 (Robin)：

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

輸出：

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

輸入 (Ben)：

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

輸出：

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

接下來，我們會新增人員之間關聯性的邊緣。

輸入 (Thomas -> Mary Kay)：

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

輸出：

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

輸入 (Thomas -> Robin)：

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

輸出：

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

輸入 (Robin -> Ben)：

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

輸出：

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>更新頂點

我們會以新的年齡 45 更新 Thomas 頂點。

輸入：
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
輸出：

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>查詢圖形

現在，我們會對您的圖形執行各種查詢。

首先，我們會使用篩選條件嘗試查詢，只傳回超過 40 歲的人員。

輸入 (篩選查詢)︰

```
:> g.V().hasLabel('person').has('age', gt(40))
```

輸出：

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

接下來，我們會預測超過 40 歲的第一個人員名稱。

輸入 (篩選 + 預測查詢)：

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

輸出：

```
==>Thomas
```

## <a name="traverse-your-graph"></a>查詢圖形

我們會周遊圖形，以傳回 Thomas 的所有朋友。

輸入 (Thomas 的朋友)：

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

輸出： 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

接下來，我們會取得下一層的頂點。 周遊圖形，以傳回 Thomas 朋友的所有朋友。

輸入 (Thomas 朋友的朋友)：

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
輸出：

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>刪除頂點

我們現在會從圖形資料庫中刪除頂點。

輸入 (刪除 Robin 頂點)︰

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>清除圖形

最後，我們會清除所有頂點和邊緣的資料庫。

輸入：

```
:> g.V().drop()
```

恭喜！ 您已經完成此 Azure Cosmos DB：圖形 API 教學課程！

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源：  

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形、如何建立頂點和邊緣，以及如何使用 Gremlin 主控台來周遊圖形。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)
