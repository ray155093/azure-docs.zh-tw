---
title: 如何建立媒體處理器 | Microsoft Docs
description: 了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="how-to:-get-a-media-processor-instance"></a>如何：取得媒體處理器執行個體
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overview
在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。 您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

| 媒體處理器名稱 | 說明 | 相關資訊 |
| --- | --- | --- |
| Media Encoder Standard |提供隨選編碼的標準功能。 |[Azure 隨選媒體編碼器的概觀和比較](media-services-encode-asset.md) |
| Media Encoder Premium Workflow |可讓您使用 Media Encoder Premium Workflow 執行編碼工作。 |[Azure 隨選媒體編碼器的概觀和比較](media-services-encode-asset.md) |
| Azure Media Indexer |可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。 |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (預覽) |可讓您使用影片穩定讓影片中的「巔簸」變得平滑。 也可讓您將內容加速至可使用的片段。 |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |停用 | |
| Storage Decryption |停用 | |
| Azure Media Packager |停用 | |
| Azure Media Encryptor |停用 | |

## <a name="get-mediaprocessor"></a>取得 MediaProcessor
> [!NOTE]
> 使用媒體服務 REST API 時，適用下列考量事項：
> 
> 在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。 如需詳細資訊，請參閱 [媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。
> 
> 順利連接到 https://media.windows.net 之後，您會收到 301 重新導向，指定另一個媒體服務 URI。 您必須依照 [使用 REST API 連線至媒體服務](media-services-rest-connect-programmatically.md)所述，對新的 URI 進行後續呼叫。 
> 
> 

下列 REST 呼叫示範如何依名稱取得媒體處理器執行個體 (在此案例中， **媒體編碼器標準**)。 

要求：

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

回應：

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟
既然您已了解如何取得媒體處理器執行個體，請移至 [如何為資產編碼](media-services-rest-get-started.md) 主題，以了解如何使用媒體編碼器標準將資產編碼。

<!--HONumber=Oct16_HO2-->


