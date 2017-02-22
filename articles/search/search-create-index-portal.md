---
title: "使用 Azure 入口網站建立 Azure 搜尋服務索引 | Microsoft Docs"
description: "使用 Azure 入口網站建立索引。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: cc439b5da8753b41f8d65781c567b4808f385078


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure 搜尋服務索引
> [!div class="op_single_selector"]
> * [概觀](search-what-is-an-index.md)
> * [入口網站](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

本文將逐步引導您完成使用 Azure 入口網站建立 Azure 搜尋服務 [索引](search-what-is-an-index.md) 的程序。

在按照本指南進行並建立索引錢，請先 [建立好 Azure 搜尋服務](search-create-service-portal.md)。

## <a name="go-to-your-azure-search-blade"></a>前往 Azure 搜尋服務刀鋒視窗
1. 按一下 [Azure 入口網站](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)左側功能表中的 [所有資源]
2. 選取您的 Azure 搜尋服務

## <a name="add-and-name-your-index"></a>新增索引並為其命名
1. 按一下 [加入索引] 按鈕
2. 為 Azure 搜尋服務索引命名。 我們正在建立索引來搜尋本指南中的旅館，因此我們將索引命名為 "hotels"。
   * 索引名稱必須以字母開頭，並僅包含小寫字母、數字或連字號 ("-")。
   * 與您的服務名稱類似，您所選擇的索引名稱也將是您為 Azure 搜尋服務 API 傳送 HTTP 要求所在端點 URL 的一部分
3. 按一下 [欄位] 項目，以開啟新的刀鋒視窗

![](./media/search-create-index-portal/add-index.png)

## <a name="create-and-define-the-fields-of-your-index"></a>建立並定義索引的欄位
1. 選取 [欄位] 項目時，將的刀鋒視窗將會以表單形式開啟，以輸入您的索引定義。
2. 使用表單將欄位新增至您的索引。
   
   * 對於每個 Azure 搜尋服務索引而言，Edm.String 類型的「索引鍵」  欄位是必要的。 此索引鍵欄位預設會使用欄位名稱 "id" 建立。 我們在我們的索引中，已將它變更為 "hotelId"。
   * 某些索引結構描述的屬性只能設定一次，而且之後無法更新。 因此，目前無法在初始設定之後進行需要重新編製索引的任何結構描述更新 (例如，變更欄位類型)。
   * 我們已經根據每個欄位在應用程式中的可能使用方式，仔細選擇其各自的屬性值。 由於必須為每個欄位指派[適當的屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)，因此在設計索引時，請牢記搜尋服務使用者體驗和商務需求。 這些屬性可控制要對哪些欄位套用哪些搜尋功能 (篩選、面向設定、排序、全文檢索搜尋等)。 例如，搜尋旅館的人很可能會對 [說明] 欄位上的關鍵字相符項目感興趣，因此我們藉由將設定 "Searchable" 屬性來啟用該欄位的全文檢索搜尋。
     * 您也可以按一下刀鋒視窗頂端的 [分析器]索引標籤，為每個欄位設定[語言分析器](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx)。 您可以在下面看到，我們已經在索引中，為用於法文文字的欄位選取法文分析器。
3. 按一下 [欄位] 刀鋒視窗上的 [確定]  來確認您的欄位定義
4. 按一下 [新增索引] 刀鋒視窗上的 [確定]  來儲存並建立您剛才定義的索引。

在以下的螢幕擷取畫面中，您可以看到我們如何為"hotels" 索引命名並定義欄位。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>後續步驟
建立 Azure 搜尋服務索引後，您就可以 [將內容上傳到索引](search-what-is-data-import.md) ，以便開始搜尋資料。




<!--HONumber=Jan17_HO2-->


