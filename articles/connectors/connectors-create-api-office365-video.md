---
title: "在您的邏輯應用程式中新增 Office 365 影片連接器 | Microsoft Docs"
description: "開始在您的 Microsoft Azure App Service Logic Apps 中使用 Office 365 影片連接器"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 959eaca7aafd8516cfc6f3b5dd2a44ce21ec4825


---
# <a name="get-started-with-the-office365-video-connector"></a>開始使用 Office 365 影片連接器
連接至 Office 365 影片，以取得 Office 365 影片的相關資訊、影片清單等。 您可以從下列應用程式使用 Office 365 影片連接器︰

* 邏輯應用程式 

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。 所有的舊版結構描述均不支援此連接器。
> 
> 

有了 Office 365 影片，您可以：

* 根據您從 Office 365 影片所取得的資料，來建置您的商務流程。 
* 檢查影片入口網站狀態、取得頻道影片清單等動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 例如，您可以使用 Bing 搜尋連接器來搜尋 Office 365 影片，然後使用 Office 365 影片連接器取得該影片的相關資訊。 如果影片符合您的需求，您可以將該影片張貼在 Facebook 上。 

如要在邏輯應用程式中新增作業，請參閱 [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Office 365 影片連接器提供下列動作。 但不包含觸發程序。

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>檢查視訊入口網站狀態</li><li>取得所有可檢視的頻道</li><li>取得某個影片之 Azure 媒體服務資訊清單的播放 URL</li><li>取得有權限來為影片解密的持有人權杖</li><li>取得特定 Office 365 影片的相關資訊</li><li>列出某個頻道中的所有 Office 365 影片</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。 

## <a name="create-a-connection-to-office365-video-connector"></a>建立至 Office365 影片連接器的連線
當您將這個連接器新增到邏輯應用程式時，您必須登入您的 Office 365 影片帳戶，並允許邏輯應用程式連線到您的帳戶。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

當您建立連線之後，請輸入 Office 365 影片的屬性，例租用戶名稱或頻道識別碼。 本主題的＜REST API 參考＞  一節會說明這些屬性。

> [!TIP]
> 您可以在其他邏輯應用程式中，使用這個相同的 Office 365 影片連線。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參考
適用的版本：1.0。

### <a name="checks-video-portal-status"></a>檢查視訊入口網站狀態
查看影片入口網站的狀態，來確認影片服務是否已啟用。  
```GET: /{tenant}/IsEnabled``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="get-all-viewable-channels"></a>取得所有可檢視的頻道
取得使用者可檢視的所有頻道。  
```GET: /{tenant}/Channels``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>列出某個頻道中的所有 Office 365 影片
列出某個頻道中的所有 office365 影片。  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |
| channelId |字串 |是 |路徑 |None |需要擷取之影片所屬的頻道識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="gets-information-about-a-particular-office365-video"></a>取得特定 Office 365 影片的相關資訊
取得特定 office365 影片的相關資訊。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |
| channelId |字串 |是 |路徑 |None |頻道識別碼 |
| videoId |字串 |是 |路徑 |None |視訊的識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>取得某個影片之 Azure 媒體服務資訊清單的播放 URL
取得某個影片之 Azure 媒體服務資訊清單的播放 URL。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |
| channelId |字串 |是 |路徑 |None |頻道識別碼 |
| videoId |字串 |是 |路徑 |None |視訊的識別碼 |
| streamingFormatType |字串 |yes |query |None |串流格式類型。 1：Smooth Streaming 或 MPEG-DASH。 0：HLS Streaming |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>取得有權限來為影片解密的持有人權杖
取得權限為影片解密的持有人權杖。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| tenant |字串 |是 |路徑 |None |使用者所屬目錄的租用戶名稱 |
| channelId |字串 |是 |路徑 |None |頻道識別碼 |
| videoId |字串 |是 |路徑 |None |視訊的識別碼 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 404 |找不到 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
#### <a name="channel-channel-class"></a>頻道：頻道類別
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| 課程名稱 |字串 |no |
| 說明 |字串 |no |

#### <a name="video"></a>影片
| 名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| id |字串 |no |
| 課程名稱 |字串 |no |
| 說明 |字串 |no |
| CreationDate |字串 |no |
| 擁有者 |字串 |no |
| ThumbnailUrl |字串 |no |
| VideoUrl |字串 |no |
| VideoDuration |integer |no |
| VideoProcessingStatus |integer |no |
| ViewCount |integer |no |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。




<!--HONumber=Nov16_HO3-->


