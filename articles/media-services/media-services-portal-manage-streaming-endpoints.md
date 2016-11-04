---
title: 透過 Azure 入口網站管理串流端點 | Microsoft Docs
description: 本主題說明如何透過 Azure 入口網站管理串流端點。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
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
# 透過 Azure 入口網站管理串流端點
## Overview
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> 
> 

在 Microsoft Azure 媒體服務中，「串流端點」代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。媒體服務也提供順暢的 Azure CDN 整合。來自 StreamingEndpoint 服務的輸出資料流可以是即時資料流，也可以是媒體服務帳戶中的隨選視訊資產。

此外，您可以藉由調整串流單位，控制串流端點服務如何應付不斷增加的頻寬需求。建議您為生產環境中的應用程式配置一個或多個縮放單位。利用串流單位，我們可以購買專用的流出容量 (以 200 Mbps 為單位逐次增加) 以及其他包含以下幾點的功能：[動態封裝](media-services-dynamic-packaging-overview.md)、CDN 整合及進階組態。

> [!NOTE]
> 只有當串流端點處於執行中狀態時，才會向您收取費用。
> 
> 

本主題簡介串流端點提供的主要功能。本主題也會示範如何使用 Azure 入口網站來管理串流端點。如需調整串流端點的相關資訊，請參閱[這個](media-services-portal-scale-streaming-endpoints.md)主題。

## 開始管理串流端點
若要開始管理您帳戶的串流端點，請執行下列作業。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [設定] 視窗中，選取 [串流端點]。
   
    ![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

## 新增/移除串流端點
若要使用 Azure 入口網站來新增或移除串流端點，以執行下列作業：

1. 若要新增串流端點，請按一下頁面頂端的 [+端點]。
2. 若要刪除串流端點，請按下 [刪除] 按鈕。
   
    預設串流端點不可刪除。
3. 按一下 [啟動] 按鈕以啟動串流端點。
   
    ![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

根據預設，您可以建立兩個串流端點。如果您需要更多串流端點，請參閱[配額和限制](media-services-quotas-and-limitations.md)。

## <a id="configure_streaming_endpoints"></a>設定串流端點
當您至少擁有一個調整單位時，串流端點可讓您設定下列屬性：

* 存取控制
* 快取控制
* 跨站台存取原則

如需這些屬性的詳細資訊，請參閱 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)。

若要設定串流端點，請執行以下作業：

1. 選取您想要設定的串流端點。
2. 按一下 [設定]。

隨時顯示簡要的欄位說明。

![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 最大快取原則：用來設定透過此串流端點所提供的資源快取存留期。如果沒有設定任何值，則會使用預設值。預設值也可以直接在 Azure 儲存體中定義。如果 Azure CDN 已針對串流端點啟用，您便不應該將快取原則值設定成少於 600 秒。
2. 允許的 IP 位址：用來指定能夠連接到已發佈之串流端點的 IP 位址。若未指定 IP 位址，則任何 IP 位址都可連接。IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1(255.255.255.0)’)。
3. 針對 Akamai 簽章標頭驗證的設定：用來指定來自 Akamai 伺服器的簽章標頭驗證的設定方式。到期時間的格式為 UTC。

## <a id="enable_cdn"></a>啟用 Azure CDN 整合
您可以為串流端點指定 Azure CDN 整合 (預設為停用)。

若要將 Azure CDN 整合設定成 true：

* 串流端點必須具有至少一個串流單位。如果以後想要將調整單位設定為 0，請先停用 CDN 整合。根據預設，當您建立新的串流端點時也會自動設定一個串流單位。
* 串流端點必須處於停止狀態。啟用 CDN 之後，您可以啟動串流端點。

您可能需要等候 90 分鐘的時間，才能啟用 Azure CDN 整合。它需要兩個小時才能讓變更跨所有 CDN POP 生效。

CDN 整合已在所有 Azure 資料中心啟用：美國西部、美國東部、北歐、西歐、日本西部、日本東部、東南亞和東亞。

一旦啟用，[存取控制] 設定將會停用。

![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

> [!IMPORTANT]
> Azure 媒體服務與 Azure CDN 的整合會在「來自 Verizon 的 Azure CDN」上實作。如果您想要將「來自 Akamai 的 Azure CDN」用於 Azure 媒體服務，您必須[手動設定端點](../cdn/cdn-create-new-endpoint.md)。如需 Azure CDN 功能的詳細資訊，請參閱 [CDN 概觀](../cdn/cdn-overview.md)。
> 
> 

### 其他考量
* 對串流端點啟用 CDN 時，用戶端無法直接從來源要求內容。如果您需要在具有 CDN 或不具有 CDN 的情況下測試您內容的能力，您可以建立另一個未啟用 CDN 的串流端點。
* 您的串流端點主機名稱在啟用 CDN 之後維持不變。您在啟用 CDN 之後不需要對您的媒體服務工作流程進行任何變更。例如，如果您的串流端點主機名稱是 strasbourg.streaming.mediaservices.windows.net，則在啟用 CDN 之後，會使用完全相同的主機名稱。
* 對於新的串流端點，您可以藉由建立新的端點來啟用 CDN；對於現有的串流端點，您必須先停止端點，然後再啟用 CDN。

如需詳細資訊，請參閱[宣佈 Azure 媒體服務與 Azure CDN (內容傳遞網路) 的整合](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)。

## 後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->