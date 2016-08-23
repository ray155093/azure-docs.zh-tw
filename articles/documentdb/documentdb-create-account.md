<properties
	pageTitle="如何建立 DocumentDB 帳戶 | Microsoft Azure"
	description="使用 DocumentDB 建立 NoSQL 資料庫。遵循下列指示來建立 DocumentDB 帳戶，並開始建立速度超快、全球規模的 NoSQL 資料庫。" 
	keywords="建立資料庫"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/11/2016"
	ms.author="mimig"/>

# 如何使用 Azure 入口網站建立 DocumentDB 帳戶

> [AZURE.SELECTOR]
- [Azure 入口網站](documentdb-create-account.md)
- [Azure CLI 和 ARM](documentdb-automation-resource-manager-cli.md)

若要使用 Microsoft Azure DocumentDB 建立資料庫，您必須：

- 具有 Azure 帳戶。您可以取得[免費 Azure 帳戶](https://azure.microsoft.com/free) (若您尚無此帳戶)。
- 建立 DocumentDB 帳戶。

您可以使用 Azure 入口網站、Azure Resource Manager 範本或 Azure 命令列介面 (CLI) 來建立 DocumentDB 帳戶。本文說明如何使用在 Azure 入口網站中建立資料庫帳戶。若要使用 Azure 資源管理員或 Azure CLI 建立帳戶，請參閱[自動建立 DocumentDB 資料庫帳戶](documentdb-automation-resource-manager-cli.md)。

您是 DocumentDB 的新手嗎？ 觀看 Scott Hanselman 製作的[這部](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/)四分鐘的影片，以了解如何在線上入口網站中完成最常見工作。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## 後續步驟

您已有了 DocumentDB 帳戶，下一步是建立 DocumentDB 資料庫。您可以使用下列其中一個動作來建立資料庫：

- Azure 入口網站，如[使用 Azure 入口網站建立 DocumentDB 資料庫](documentdb-create-database.md)中所述。
- GitHub 上 [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 儲存機制之 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 專案中的 C# .NET 範例。
- 全部包含的教學課程：[.NET](documentdb-get-started.md)、[.NET MVC](documentdb-dotnet-application.md)、[Java](documentdb-java-application.md)、[Node.js](documentdb-nodejs-application.md) 或 [Python](documentdb-python-application.md)。
- [DocumentDB SDK](documentdb-sdk-dotnet.md)。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。


建立您的資料庫之後, 您必須[加入一或多個集合](documentdb-create-collection.md)至資料庫，然後[加入文件](documentdb-view-json-document-explorer.md)至集合。

在集合中有了文件之後，您可以使用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](documentdb-sdk-dotnet.md)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

若要深入了解 DocumentDB，請探索以下資源：

-	[DocumentDB 的學習途徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB 資源模型和概念](documentdb-resources.md)

<!---HONumber=AcomDC_0817_2016-->