---
title: "Azure DocumentDB 的佈建輸送量 | Microsoft Docs"
description: "了解如何設定 DocumentDB 集合的佈建輸送量。"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: abdf0af8a85db19c68a0d74c0477d798c0fd03fc
ms.openlocfilehash: 8ff2fc6106438e35b93112a6dc97814ba79b06fc
ms.lasthandoff: 02/22/2017


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>設定 Azure DocumentDB 集合的輸送量

您可以在 Azure 入口網站，或是使用用戶端 SDK，設定 DocumentDB 集合的輸送量。 

下表列出集合可使用的輸送量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一分割區集合</strong></p></td>
            <td valign="top"><p><strong>已分割集合</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量下限</p></td>
            <td valign="top"><p>每秒&400; 個要求單位</p></td>
            <td valign="top"><p>每秒&2;,500 個要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量上限</p></td>
            <td valign="top"><p>每秒&10;,000 個要求單位</p></td>
            <td valign="top"><p>無限制</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> 若要將資料分割的集合設定為輸送量值介於 2,500 RU/秒和 10,000 RU/秒，您必須暫時使用 Azure 入口網站。 SDK 中尚無法使用此功能。

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>使用 Azure 入口網站設定輸送量

1. 在新的視窗中，開啟 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側的 [NoSQL (DocumentDB)]，或依序按一下底部的 [更多服務]，捲動至 [資料庫]，按一下 [NoSQL (DocumentDB)]。
3. 選取您的 DocumentDB 帳戶。
4. 在新的視窗中，按一下 [集合] 下方的 [調整]，如下列螢幕擷取畫面所示。
5. 在新的視窗中，從下拉式清單選取您的集合，變更 [輸送量] 值，然後按一下 [儲存]。

    ![顯示如何在 Azure 入口網站中瀏覽至您的帳戶並按一下 [調整]，來變更集合輸送量的螢幕擷取畫面](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>使用 .NET SDK 入口網站設定輸送量

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>輸送量常見問題集

**我可以將輸送量設定為小於 400 RU/s 嗎？**

400 RU/s 是 DocumentDB 單一分割區集合上可用的最小輸送量 (2500 RU/s 是分割區集合的最小值)。 要求單位是設定為 100 RU/s 時間間隔，但不能將輸送量設定為 100 RU/s 或任何小於 400 RU/s 的值。 如果您正在尋找符合成本效益的方法來開發和測試 DocumentDB，您可以使用免費的 [DocumentDB 模擬器](documentdb-nosql-local-emulator.md)，於本機免費部署此模擬器。 

## <a name="next-steps"></a>後續步驟

若要深入了解 DocumentDB 的佈建和全球規模化，請參閱 [DocumentDB 的資料分割和調整規模](documentdb-partition-data.md)。

