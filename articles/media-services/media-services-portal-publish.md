---
title: " 透過 Azure 入口網站發佈內容 | Microsoft Docs"
description: 本教學課程將逐步引導您完成透過 Azure 入口網站發佈內容的步驟。
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
ms.date: 08/29/2016
ms.author: juliako

---
# 透過 Azure 入口網站發佈內容
> [!div class="op_single_selector"]
> * [入口網站](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## Overview
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> 
> 

如要想提供 URL 給使用者，讓使用者可以利用這個 URL 來傳送或下載內容，請您先建立定位器來發行您的資產。定位器提供對於資產中包含之檔案的存取。媒體服務支援兩種類型的定位器：

* 串流 (OnDemandOrigin) 定位器，用來自適性串流 (例如，串流處理 MPEG DASH、HLS 或 Smooth Streaming)。若要建立串流定位器，您的資產必須包含 .ism 檔案。
* 漸進式 (SAS) 定位器，用於透過漸進式下載傳遞視訊。

資料流 URL 的格式如下，還可以用它來播放 Smooth Streaming 資產。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建立 HLS 資料流 URL，請在 URL 加上 (format=m3u8-aapl)。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建置 MPEG DASH 串流 URL，請將 (格式=mpd-time-csf) 附加至 URL。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SAS URL 具有下列格式。

    {blob container name}/{asset name}/{file name}/{SAS signature}

如需詳細資訊，請參閱[傳遞內容概觀](media-services-deliver-content-overview.md)。

> [!NOTE]
> 如果您在 2015 年 3 月前使用入口網站來建立定位器，則建立的定位器 2 年後便告失效。
> 
> 

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。

### 使用入口網站發佈資產
若要使用入口網站發佈資產，請執行下列作業：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [設定] > [資產]。
3. 選取您要發佈的資產。
4. 按一下 [發佈] 按鈕。
5. 選取定位器類型。
6. 按 [新增]。
   
    ![發佈](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 便會加入 [已發佈的 URL] 清單中。

## 從入口網站播放內容
Azure 入口網站提供內容播放程式，您可用來測試您的視訊。

按一下想要的視訊，然後按一下 [播放] 按鈕。

![發佈](./media/media-services-portal-vod-get-started/media-services-play.png)

適用一些考量事項：

* 確定已發佈視訊。
* 此**媒體播放器**會從預設串流端點播放。如果您想要從非預設串流端點播放，請按一下以複製 URL 並使用其他播放器。例如，[Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
* 您正在進行串流的串流端點必須正在執行。
* 若要從串流端點進行串流，您必須新增至少一個串流單位。如需詳細資訊，請參閱[這個](media-services-portal-scale-streaming-endpoints.md)主題。

## 後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->