---
title: "Azure 隨選媒體編碼器的概觀和比較 | Microsoft Docs"
description: "本主題概要說明並比較 Azure 隨選媒體編碼器。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0124c884d3d805a407041e8ecbc5961209a9653d
ms.openlocfilehash: 84754f5ef9da99b489fd6d50ddcfe8494b57b396


---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Azure 隨選媒體編碼器的概觀和比較
## <a name="encoding-overview"></a>編碼概觀
Azure 媒體服務提供多個用於將雲端中之媒體編碼的選項。

開始使用媒體服務時，請務必了解轉碼器和檔案格式之間的差異。
轉碼器是實作壓縮/解壓縮演算法的軟體，而檔案格式是保存已壓縮視訊的容器。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用 [動態封裝](media-services-dynamic-packaging-overview.md)，您需要執行下列動作：

* 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟稍後示範於本教學課程中)。
* 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。 如需詳細資訊，請參閱 [如何調整隨選串流保留單元](media-services-portal-manage-streaming-endpoints.md)。

媒體服務支援本文中所描述的下列隨選編碼器：

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

本文概略敘述隨選媒體編碼器，並提供文章連結以提供更詳細資訊。 本主題也提供各種編碼器的比較。

請注意，每個媒體服務帳戶預設一次可以有一個進行中的編碼工作。 您可以保留編碼單位，這樣就可以同時執行多個編碼工作，其中一個用於您購買的每一個編碼保留單位。 如需相關資訊，請參閱 [調整編碼單位](media-services-scale-media-processing-overview.md)。

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>使用方式
[如何使用 Media Encoder Standard 進行編碼](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>格式
[格式和轉碼器](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>預設值
Media Encoder Standard 使用 [這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

### <a name="input-and-output-metadata"></a>輸入和輸出中繼資料
[這裡](http://msdn.microsoft.com/library/azure/dn783120.aspx)說明編碼器輸入中繼資料。

[這裡](http://msdn.microsoft.com/library/azure/dn783217.aspx)說明編碼器輸出中繼資料。

### <a name="generate-thumbnails"></a>產生縮圖
如需相關資訊，請參閱 [如何使用媒體編碼器標準產生縮圖](media-services-custom-mes-presets-with-dotnet.md#thumbnails)。

### <a name="trim-videos-clipping"></a>修剪視訊 (裁剪)
如需相關資訊，請參閱 [如何使用媒體編碼器標準修剪視訊](media-services-custom-mes-presets-with-dotnet.md#trim_video)。

### <a name="create-overlays"></a>建立疊加層
如需相關資訊，請參閱 [如何使用媒體編碼器標準建立覆疊](media-services-custom-mes-presets-with-dotnet.md#overlay)。

### <a name="see-also"></a>另請參閱
[媒體服務部落格](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium Workflow
### <a name="overview"></a>Overview
[介紹 Azure 媒體服務中的 Premium 編碼](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>使用方式
Media Encoder Premium Workflow 使用複雜的工作流程設定。 您可以使用 [工作流程設計工具](media-services-workflow-designer.md) 建立和更新工作流程檔案。

[如何使用 Azure 媒體服務中的 Premium 編碼](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>已知問題
如果您的輸入視訊不包含隱藏式字幕，輸出資產仍然會包含空白 TTML 檔案。 


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相關文章
* [透過自訂 Media Encoder Standard 預設值來執行進階編碼工作](media-services-custom-mes-presets-with-dotnet.md)
* [配額和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Nov16_HO5-->


