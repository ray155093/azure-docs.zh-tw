---
title: " 透過 Azure 入口網站調整串流端點 | Microsoft Docs"
description: "本教學課程將逐步引導您完成使用 Azure 入口網站調整串流端點的步驟。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: f9f7872eab6b61afcbdc2d8eb23f1dc8ada82829


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>透過 Azure 入口網站調整串流端點
## <a name="overview"></a>概觀
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

使用 Azure 媒體服務時，其中一個最常見案例是透過自適性串流提供影片給您的用戶端。 媒體服務支援下列自適性串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DAS。

媒體服務提供動態封裝，這讓您以媒體服務即時支援的串流格式 (MPEG DASH、HLS、Smooth Streaming) 提供自適性 MP4 編碼內容，而不必儲存這些串流格式個別的預先封裝版本。

若要利用動態封裝，您需要執行下列動作：

* 將您的夾層 (來源) 檔編碼為一組自適性 MP4 檔案 (編碼步驟稍後示範於本教學課程中)。  
* 為您計畫從該處傳遞內容的「串流端點」  至少建立一個串流單位。 以下步驟顯示如何變更串流單位數目。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

此外，您可以藉由調整串流單位，控制串流端點服務如何應付不斷增加的頻寬需求。 建議您為生產環境中的應用程式配置一個或多個縮放單位。 利用串流單位，我們可以購買專用的流出容量 (以 200 Mbps 為單位逐次增加) 以及其他包含以下幾點的功能： [動態封裝](media-services-dynamic-packaging-overview.md)、CDN 整合及進階組態。 如需詳細資訊，請參閱 [使用 Azure 入口網站管理串流端點](media-services-portal-manage-streaming-endpoints.md)。

## <a name="scale-streaming-endpoints"></a>調整串流端點
若要建立和變更串流保留單位數目，請執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [設定] 視窗中，選取 [串流端點]。
3. 按一下您要調整的串流端點。 
4. 移動滑桿以指定串流單位數目

![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

您必須考量下列事項：

* 任何新的串流單位配置可能會花費大約 20 分鐘的時間才能完成。 
* 目前，串流單位從任何正數值到無，都可能停用隨選串流長達一小時。
* 計算成本時會使用 24 小時內指定的最大單位數。 如需定價詳細資料的相關資訊，請參閱＜ [媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)＞。

## <a name="next-steps"></a>後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Dec16_HO2-->


