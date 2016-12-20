---
title: "依國家/地區限制您的 Azure CDN 內容的存取 | Microsoft Docs"
description: "了解如何使用地區篩選功能限制您的 Azure CDN 內容存取。"
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1dfd5833d8637232b7998b7fdc7f39679645e1f7


---
# <a name="restrict-access-to-your-content-by-country---akamai"></a>依國家 (地區) 限制存取您的內容 - Akamai
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai 標準](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>Overview
當使用者要求您的內容時，預設會提供內容，不論使用者從哪裡提出這項要求。 在某些情況下，您可能想要依國家 (地區) 限制存取您的內容。 本主題說明如何使用**地區篩選**功能設定服務，以依國家 (地區) 允許或封鎖存取。

> [!IMPORTANT]
> Verizon 和 Akamai 產品提供相同地理篩選功能，但兩者的使用者介面不同。 本文件描述 **來自 Akamai 的 Azure CDN 標準**的介面。 針對具有 **Verizon 的 Azure CDN 標準/高階**的地區篩選，請參閱[依國家 (地區) 限制存取您的內容 - Verizon](cdn-restrict-access-by-country.md)。
> 
> 

如需適用於設定此類限制之考量的資訊，請參閱本主題結尾的 [考量](cdn-restrict-access-by-country.md#considerations) 一節。  

![國家 (地區) 篩選](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>步驟 1：定義目錄路徑
選取您在入口網站內的端點，然後尋找左邊導覽上的 [地區篩選] 索引標籤，以找到這項功能。

在設定國家 (地區) 篩選器時，您必須指定要允許或拒絕使用者存取之位置的相對路徑。 您可以使用 "/"，或藉由指定目錄路徑 "/pictures/" 來選取資料夾，為所有檔案套用地區篩選。 您也可以將地區篩選套用至單一檔案，方法是指定檔案，並保留結尾斜線 "/pictures/city.png"。

目錄路徑篩選器範例：

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>步驟 2：定義動作：封鎖或允許
**封鎖：** 將拒絕來自指定國家 (地區) 的使用者，存取從該遞迴路徑要求的資產。 若尚未設定該位置的其他國家 (地區) 篩選選項，則其他所有使用者都將允許存取。

**允許：** 只允許來自指定國家 (地區) 的使用者存取從該遞迴路徑要求的資產。

## <a name="step-3-define-the-countries"></a>步驟 3：定義國家 (地區)
選取您想要封鎖或允許路徑的國家 (地區)。 如需詳細資訊，請參閱[來自 Akamai 的 Azure CDN 國家/地區代碼](https://msdn.microsoft.com/library/mt761717.aspx)。

例如，封鎖 /Photos/Strasbourg/ 的規則會篩選檔案，包括：

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>國碼
**地區篩選**功能可使用國碼 (地區碼) 來定義國家 (地區)，以允許或封鎖該國家 (地區) 對受保護目錄的要求。 您可以在 [來自 Akamai 的 Azure CDN 國家 (地區) 代碼](https://msdn.microsoft.com/library/mt761717.aspx)找到國家 (地區) 代碼。 

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>考量
* 最多可能需要數分鐘，您對國家 (地區) 篩選組態的變更才會生效。
* 這項功能不支援萬用字元 (例如，‘*’)。
* 會將相對路徑相關聯的地區篩選組態，遞迴地套用到該路徑。
* 只有一個規則可套用至相同的相對路徑，您無法建立指向相同之相對路徑的多個國家 (地區) 篩選。 不過，一個資料夾可有多個國家 (地區) 篩選。 這是因為國家 (地區) 篩選的遞迴本質。 換句話說，可以將不同的國家 (地區) 篩選指派給先前設定之資料夾的子資料夾。




<!--HONumber=Nov16_HO3-->


