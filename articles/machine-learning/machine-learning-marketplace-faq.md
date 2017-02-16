---
title: "(已過時) 常見問題集 - 在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式 | Microsoft Docs"
description: "(已過時) 有關在 Azure Marketplace 中發佈 Machine Learning 應用程式的常見問題集"
services: machine-learning
documentationcenter: 
author: bharaths
manager: jhubbard
editor: cgronlun
ms.assetid: 26b3a1e7-8b9a-4004-98bc-17456d4c25e8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 9e2d4a2d46bd461a71217cd2b2d75f5a50e8b617


---
# <a name="deprecated-publishing-and-using-machine-learning-apps-in-the-azure-marketplace-faq"></a>(已過時) 在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式：常見問題集

> [!NOTE]
> DataMarket 和「資料服務」已進入淘汰階段，訂用帳戶將自 2017 年 3 月 31 日起淘汰並取消。 因此，這篇文章目前已過時。 
> 
> 替代方案是，您可以將 Machine Learning 實驗發佈到 [Cortana Intelligence 資源庫](https://gallery.cortanaintelligence.com/)，以利資料科學社群使用。 如需詳細資訊，請參閱[在 Cortana Intelligence 資源庫中共用及探索資源](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish)。


## <a name="questions-about-consuming-from-marketplace"></a>從 Marketplace 取用的相關問題
**1.為什麼我輸入 Web 服務的輸入之後，得到下列錯誤訊息：**

**要求導致後端逾時或後端錯誤。小組正在調查這個問題。很抱歉造成您的不便。(500)**

您的輸入參數可能不符合特定 Web 服務所需的格式。 請參閱對應的文件連結，來尋找輸入參數的正確格式以及此 Web 服務的限制。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2.如果我將 [探索這個資料集] 頁面上顯示之 Web 服務的 API 連結，複製並貼到另一個瀏覽器視窗中，我應該使用哪些認證來存取結果，又該如何查看這些結果？**

您應該使用您的 Marketplace 帳戶作為使用者名稱，並使用主要帳戶金鑰作為密碼。 您可以在 Web 服務描述下的 [探索這個資料集] 頁面上，找到主要帳戶金鑰 (按一下 [顯示] 按鈕)。 視您所使用的瀏覽器而定，結果可能會顯示在瀏覽器中或可供下載。

**3.為什麼我輸入 Web 服務的輸入之後，在 [探索這個資料集] 頁面上得到下列錯誤訊息：** 

**處理您的要求時發生未預期的錯誤。請再試一次。**

在 Marketplace 的 [探索這個資料集]  頁面上取用您的 Web 服務時，該服務的一個或多個輸入參數可能超過長度限制。 您可以透過 HTTP POST 方法，使用較長的輸入長度呼叫服務。 例如，請參閱 [使用 R 在 Machine Learning 上建置並發佈至 Marketplace 的範例解決方案](machine-learning-r-csharp-web-service-examples.md)。

**4.為什麼 Azure 傳統入口網站之 [存放區] 的 [API 總管] 索引標籤中未顯示任何項目？** 

這是 Azure 傳統入口網站 Marketplace 的已知問題。 小組正著手解決這個問題。 

## <a name="questions-about-publishing-from-azure-machine-learning-on-marketplace"></a>透過 Azure Machine Learning 在 Marketplace 上發佈的相關問題
**1.為什麼我的 Web 服務不會重新整理標誌或影像的異動？** 

標誌和影像是在發佈入口網站中快取，新的標誌或影像最多可能需要 10 天才能在入口網站上更新。

**2.為什麼在 Marketplace 上，我的 Web 服務的 [詳細資料] 索引標籤顯示錯誤訊息？**

在連接到 Azure Machine Learning 以取得服務詳細資料時，已知有一個 Marketplace 問題。 小組正著手解決這個問題。

**3.為什麼在取用 Marketplace 中的 Web 服務時，無法使用 Azure Machine Learning Web 服務中的 R 範例程式碼？**

直接連接到 Azure Machine Learning Web 服務，相較於透過 Marketplace 連接到這些 Web 服務，有不同的驗證系統。 Marketplace 中的服務是 OData 服務，而且可以使用 GET 或 POST 方法進行呼叫。 

**4.為何我的 Web 服務產品的支援連結無法正確更新我的部分產品？**

支援連結是依照發佈者而非依照產品的全域連結。 

**5.如何使用 Marketplace 中的批次輸入模式來發佈 Web 服務？**

Marketplace Web 服務目前不支援批次輸入模式。

**6.如果我有關於成為資料發行者的問題，或在發佈過程中發生問題，我應該與誰連絡以取得協助？**

如需詳細資訊，請連絡 Azure Marketplace 小組 (<mailto:datamarketbd@microsoft.com>)。




<!--HONumber=Jan17_HO2-->


