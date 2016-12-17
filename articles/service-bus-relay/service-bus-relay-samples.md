---
title: "服務匯流排轉送範例概觀 | Microsoft Docs"
description: "分類及描述服務匯流排轉送範例與每個範例的連結。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b2206fae964c55e961505b9d0e2ad71fd0b2102


---
# <a name="service-bus-relay-samples"></a>服務匯流排轉送範例
服務匯流排轉送範例在[服務匯流排轉送](https://azure.microsoft.com/services/service-bus/)中示範主要功能。 本主題分類及描述可用的範例與每個範例的連結。

> [!NOTE]
> 服務匯流排範例不會與 SDK 一起安裝。 若要取得範例，請造訪 [Azure SDK 範例頁面](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)。
> 
> 此外，[這裡](https://github.com/Azure-Samples/azure-servicebus-relay-samples)提供一組更新過的服務匯流排轉送範例 (在進行這項撰寫時，本文未說明過它們)。  
> 
> 

如需訊息範例，請參閱[服務匯流排訊息範例](../service-bus-messaging/service-bus-samples.md)。

## <a name="service-bus-relay"></a>服務匯流排轉送
下列範例會說明如何撰寫使用服務匯流排轉送服務的應用程式。

請注意，轉送範例需要連接字串以存取服務匯流排命名空間。

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>取得 Azure 服務匯流排的連接字串。
1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 在左側欄中，按一下 [服務匯流排] 。
3. 按一下清單中的命名空間名稱。
4. 在命名空間刀鋒視窗中，按一下 [共用存取原則]。
5. 在 [共用存取原則] 刀鋒視窗中，按一下 **RootManageSharedAccessKey**。
6. 將連接字串複製到剪貼簿。

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>取得 Service Bus for Windows Server 的連接字串
1. 執行下列 PowerShell cmdlet：
   
    ```
    get-sbClientConfiguration
    ```
2. 將連接字串貼到範例的 App.config 檔案。

## <a name="service-bus-relay"></a>服務匯流排轉送
示範服務匯流排轉送的範例。

### <a name="getting-started"></a>開始使用
| 範例名稱 | 說明 | 最低需求 SDK 版本 | Availability |
| --- | --- | --- | --- |
| [WCF 轉送傳訊：Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |示範如何在 Azure 上執行服務匯流排用戶端和服務。 此範例會以程式設計方式設定服務匯流排。 只有環境和安全性資訊會儲存在組態檔中。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊驗證：共用的密碼](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |示範如何使用簽發者名稱和簽發者密碼來驗證服務匯流排。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊驗證：WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |示範如何公開不需要用戶端使用者驗證的 HTTP 服務。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |示範如何使用 **WebHttpRelayBinding** 繫結傳回使用 Web 程式設計模型的二進位資料。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：NetTcp Relayed](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |示範如何使用 **NetTcpRelayBinding** 繫結。 |1.8 |Microsoft Azure 服務匯流排 |

### <a name="exploring-features"></a>探索功能
示範各種服務匯流排轉送功能的範例。

| 範例名稱 | 說明 | 最低需求 SDK 版本 | Availability |
| --- | --- | --- | --- |
| [WCF 轉送傳訊驗證：簡單的 WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |示範如何使用簡式 web 權杖認證來驗證服務匯流排。 這個範例和 Echo 範例類似，只有一些變更。 具體而言，此範例會將行為加入 ServiceHost (服務) 和 ChannelFactory (用戶端) 應用程式中。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊：負載平衡](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |示範如何使用 Microsoft Azure 服務匯流排將訊息路由傳送至多個接收者。 它會顯示一個簡單服務的多個執行個體，透過 **NetTcpRelayBinding** 繫結與用戶端進行通訊 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：網路事件](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |示範如何使用 Microsoft Azure 服務匯流排上的 **NetEventRelayBinding** 繫結。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：WS2007Http 工作階段](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |示範如何使用已啟用可靠工作階段的 **WS2007HttpRelayBinding** 繫結。 它也會示範如何在組態檔中 (而不是以程式設計方式) 指定服務匯流排認證。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |示範如何使用 **WS2007HttpRelayBinding** 繫結搭配訊息安全性來保護端對端訊息，同時仍要求用戶端驗證服務匯流排。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊：中繼資料交換](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |示範如何公開使用轉送繫結的中繼資料端點。 下列轉送繫結中支援 **MetadataExchange**：**NetTcpRelayBinding**、**NetOnewayRelayBinding**、**BasicHttpRelayBinding** 和 **WS2007HttpRelayBinding**。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |示範如何設定 **NetTcpRelayBinding** 繫結來支援**混合式**連接模式，該模式會先建立轉送的連接，並盡可能自動切換至用戶端和服務之間的直接連接。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |示範如何使用 **NetTcpRelayBinding** 繫結搭配訊息安全性。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |示範如何公開及取用使用 **NetOnewayRelayBinding** 繫結的服務端點。 |1.8 |Microsoft Azure 服務匯流排 |
| [WCF 轉送傳訊繫結：WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |示範如何使用 **WS2007HttpRelayBinding** 繫結。 它會示範一個簡單的服務，不使用任何安全性選項且不要求用戶端進行驗證。 |1.8 |Microsoft Azure 服務匯流排 |

## <a name="next-steps"></a>後續步驟
請參閱下列主題以取得服務匯流排的概念性概觀。

* [服務匯流排轉送概觀](service-bus-relay-overview.md)
* [服務匯流排架構](../service-bus-messaging/service-bus-architecture.md)
* [服務匯流排基本概念](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


