---
title: "什麼是 DocumentDB：適用於 MongoDB 的 API？ | Microsoft Docs"
description: "了解 DocumentDB：適用於 MongoDB 的 API，以及如何在 Azure 雲端中輕鬆地執行現有的 MongoDB 應用程式"
keywords: "MongoDB 是什麼"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 6c143a18883c99a24264d3174df7ec214d696fb5
ms.lasthandoff: 03/08/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>什麼是 DocumentDB：適用於 MongoDB 的 API？

DocumentDB 資料庫現在可做為針對 MongoDB 所撰寫之應用程式的資料存放區。 這表示，使用 MongoDB 資料庫現有的[驅動程式](https://docs.mongodb.org/ecosystem/drivers/)，針對 MongoDB 所撰寫的應用程式現在可與 DocumentDB 通訊，並使用 DocumentDB 資料庫而非 MongoDB 資料庫。 在許多情況下，您只要變更連接字串，就可以從使用 MongoDB 切換到 DocumentDB。 使用這項功能，客戶可以輕鬆地在 Azure 雲端建置及執行 MongoDB 資料庫應用程式 - 利用 DocumentDB 完全受管理且可調整的 NoSQL 資料庫 - 同時繼續使用 MongoDB 的熟悉技能和工具。

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>使用 DocumentDB：適用於 MongoDB 的 API 有何好處？
**不需要任何伺服器管理工作** - DocumentDB 是完全受管理的服務，這表示您不需要自己管理任何基礎結構或虛擬機器。 DocumentDB 可在 20 個以上的 [Azure 區域](https://azure.microsoft.com/regions/services/)中使用。

**無限制的調整** - 您可以獨立和彈性地調整輸送量和儲存體。 您可以增加容量來輕鬆地處理每秒數百萬個要求數。

**企業級** - DocumentDB 支援多個本機複本，在面對本機和區域故障時可提供 99.99% 的可用性和資料保護。 DocumentDB 有企業級的[法規認證](https://www.microsoft.com/trustcenter)和安全性功能。 

**MongoDB 相容性** - DocumentDB：適用於 MongoDB 的 API 是專門針對與 MongoDB 的相容性而設計。 您可以使用現有的程式碼、應用程式、驅動程式和工具來使用 DocumentDB。 

在這段 Azure Friday 影片中，和 Scott Hanselman 與 DocumentDB 工程總經理 Kirill Gavrylyuk 一起深入了解。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>如何開始？
在 [Azure 入口網站](https://portal.azure.com)中建立 DocumentDB：適用於 MongoDB 的 API 帳戶，然後將連線交換到您的新帳戶。 

*這樣就大功告成了！*

如需詳細指示，請遵循[建立帳戶](documentdb-create-mongodb-account.md)和[連接至您的帳戶](documentdb-connect-mongodb-account.md)。

## <a name="next-steps"></a>後續步驟
* 依照下列[連線到 MongoDB 帳戶](documentdb-connect-mongodb-account.md)教學課程，了解如何取得您的帳戶連接字串資訊。
* 依照[使用 MongoChef 搭配使用 DocumentDB](documentdb-mongodb-mongochef.md) 教學課程來了解如何在 MongoChef 中建立 DocumentDB 資料庫和 MongoDB 應用程式之間的連線。
* 瀏覽 DocumentDB：適用於 MongoDB 的 API [範例](documentdb-mongodb-samples.md)。


