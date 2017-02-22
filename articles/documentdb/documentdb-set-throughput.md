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
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: c65c4021f5d0beda8005abdd375ac51729acf0a8
ms.openlocfilehash: 8ff246ab7d70bd00ce989d3049e016d692722d18


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

## <a name="next-steps"></a>後續步驟

若要深入了解 DocumentDB 的佈建和全球規模化，請參閱 [DocumentDB 的資料分割和調整規模](documentdb-partition-data.md)。


<!--HONumber=Feb17_HO2-->


