---
title: "透過 Azure 入口網站使用媒體編碼器標準為資產編碼 | Microsoft Docs"
description: "本教學課程將逐步引導您完成透過 Azure 入口網站使用 Media Encoder Standard 為資產編碼的步驟。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 50e9095d38c96323db3ccce4e3601eccbb9eb2ab


---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>透過 Azure 入口網站使用 Media Encoder Standard 為資產編碼
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

使用 Azure 媒體服務時，其中一個最常見案例是提供自適性串流給您的用戶端。 媒體服務支援下列自適性串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DAS。 若要針對自適性串流準備您的視訊，您必須將來源視訊編碼為多位元速率檔案。 您應該使用 [媒體編碼器標準]  編碼器來為您的視訊編碼。  

媒體服務提供動態封裝，這讓您以下列串流格式 (MPEG DASH、HLS、Smooth Streaming) 提供多位元速率 MP4，而不必重新封裝成這些串流格式。 使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

若要利用動態封裝功能，將您的來源檔編碼為一組多位元速率 MP4 檔案 (編碼步驟稍後示範於本節中)。

若要調整媒體處理，請參閱 [這個](media-services-portal-scale-media-processing.md) 主題。

## <a name="encode-with-the-azure-portal"></a>使用 Azure 入口網站進行編碼
本節說明當您利用媒體編碼器標準為您的內容編碼時，可以採取的步驟。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [設定] 視窗中，選取 [資產]。  
3. 在 [資產]  視窗中，選取您想要編碼的資產。
4. 按 [編碼]  按鈕。
5. 在 [為資產編碼]  視窗中，選取 [媒體編碼器標準] 處理器和預設值。 例如，如果您知道您的輸入視訊的解析度為 1920x1080 像素，您則可使用 [H264 多位元速率 1080p] 預設值。 如需預設值的詳細資訊，請參閱 [這篇](media-services-mes-presets-overview.md) 文章 – 務必選取最適合您的輸入視訊的預設值。 如果您有低解析度 (640x360) 視訊，則不應該使用預設 [H264 多位元速率 1080p] 預設值。
   
   為了方便管理，您可以選擇編輯輸出資產的名稱和作業的名稱。
   
   ![為資產編碼](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. 按下 [建立] 。

## <a name="next-step"></a>後續步驟
您可以透過 Azure 入口網站監視編碼作業進度，如 [這篇](media-services-portal-check-job-progress.md) 文章所述。  

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO3-->


