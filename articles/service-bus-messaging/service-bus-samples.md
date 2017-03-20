---
title: "服務匯流排傳訊範例概觀 | Microsoft Docs"
description: "分類及描述服務匯流排傳訊範例與每個範例的連結。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c
ms.lasthandoff: 11/17/2016


---
# <a name="service-bus-messaging-samples"></a>服務匯流排傳訊範例
服務匯流排傳訊範例會示範[服務匯流排](https://azure.microsoft.com/services/service-bus/) (雲端服務) 和 [Windows Server 服務匯流排](https://msdn.microsoft.com/library/dn282144.aspx)中的重要功能。 本主題分類及描述可用的範例與每個範例的連結。

> [!NOTE]
> 服務匯流排範例不會與 SDK 一起安裝。 若要取得範例，請造訪 [Azure SDK 範例頁面](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)。
> 
> 此外， [這裡](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) 提供一組更新過的服務匯流排訊息範例 (在進行這項撰寫時，本文未說明過它們)。  
> 
> 

如需轉送範例，請參閱[服務匯流排轉送範例](../service-bus-relay/service-bus-relay-samples.md)。

## <a name="service-bus-messaging"></a>服務匯流排傳訊
下列範例會說明如何撰寫使用服務匯流排傳訊的應用程式。

請注意，傳訊範例需要連接字串以存取服務匯流排命名空間。

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

### <a name="getting-started-samples"></a>開始使用範例
這些範例說明基本的傳訊功能。

| 範例名稱 | 說明 | 最低需求 SDK 版本 | Availability |
| --- | --- | --- | --- |
| [開始使用：使用佇列傳訊](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |示範如何使用 Microsoft Azure 服務匯流排傳送和接收來自佇列的訊息。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [開始使用：使用主題傳訊](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |示範如何使用 Microsoft Azure 服務匯流排傳送和接收來自包含多個訂用帳戶之主題的訊息。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |

### <a name="exploring-features"></a>探索功能
下列範例會示範服務匯流排的各種功能。

| 範例名稱 | 說明 | 最低需求 SDK 版本 | Availability |
| --- | --- | --- | --- |
| [HTTP 權杖提供者](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |示範利用服務匯流排驗證 HTTP/REST 用戶端的不同方式。 |2.1 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [服務匯流排 HTTP 用戶端](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |示範如何透過 HTTP/REST 與服務匯流排傳之間送及及接收訊息。 |2.3 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [服務匯流排自動轉送](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |示範如何將訊息從佇列、訂用帳戶或寄不出的信件佇列自動轉送到另一個佇列或主題。 它也會示範如何透過傳輸佇列將訊息傳送到佇列或主題。 |2.3 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：WCF 通道工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |示範如何透過 Windows Communication Foundation (WCF) 通道使用 Microsoft Azure 服務匯流排。 此範例會顯示如何使用 WCF 通道來透過服務匯流排佇列傳送和接收訊息。 此範例會顯示透過服務匯流排的工作階段和非工作階段通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：交易](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |示範如何使用交易範圍內的 Microsoft Azure 服務匯流排傳訊功能，確保批次傳訊作業以不可部分完成的方式認可。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：使用 REST 的管理作業](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |示範如何使用 REST 在服務匯流排上執行管理作業。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [資源提供者 REST API](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |示範如何使用新的服務匯流排 RDFE REST API 來管理命名空間和訊息實體。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：WCF 服務工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |示範如何透過 WCF 服務模型使用 Microsoft Azure 服務匯流排。 此範例示範如何透過服務匯流排佇列使用 WCF 服務模型來完成工作階段為基礎的通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：要求回應](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |示範如何使用 Microsoft Azure 服務匯流排和要求/回應功能。 此範例會示範透過服務匯流排佇列進行簡單的用戶端和伺服器通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：無效信件佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |示範如何使用 Microsoft Azure 服務匯流排和傳訊「無效信件佇列」功能。 此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：延遲的訊息](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |示範如何使用 Microsoft Azure 服務匯流排的訊息延遲功能。 此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：工作階段訊息](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |示範如何使用 Microsoft Azure 服務匯流排和傳訊工作階段功能。 此範例會示範透過服務匯流排佇列進行簡單的傳送者和接收者通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：要求回應主題](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |示範如何使用 Microsoft Azure 服務匯流排主題和訂用帳戶實作要求/回應模式。 此範例會示範透過服務匯流排主題進行簡單的用戶端和伺服器通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：要求回應佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |示範如何使用 Microsoft Azure 服務匯流排和要求/回應功能。 此範例會示範透過兩個服務匯流排佇列進行簡單的用戶端和伺服器通訊。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：重複偵測](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |示範如何搭配佇列使用 Microsoft Azure 服務匯流排重複訊息偵測。 它會建立兩個佇列，一個啟用重複偵測而另一個沒有重複偵測。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：非同步傳訊](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |示範如何使用 Microsoft Azure 服務匯流排以非同步方式傳送和接收來自佇列的訊息。 佇列提供傳送者與任意數目接收者之間的低耦合、非同步通訊 (此處為單一接收者)。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊︰進階篩選器](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |示範如何使用 Microsoft Azure 服務匯流排發佈/訂閱進階篩選器。 它會利用不同的篩選定義建立一個主題和 3 個訂用帳戶、將訊息傳送至主題，並從訂用帳戶接收所有訊息。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [代理傳訊：訊息預先擷取](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |示範如何使用 Microsoft Azure 服務匯流排的訊息預先擷取功能。 它會示範如何在接收時使用訊息預先擷取功能。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |

## <a name="service-bus-reference-tools"></a>服務匯流排參考工具
下列範例會示範服務的各種其他功能。

| 範例名稱 | 說明 | 最低需求 SDK 版本 | Availability |
| --- | --- | --- | --- |
| [服務匯流排總管](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |服務匯流排總管可讓使用者連接到服務匯流排服務命名空間並以簡便的方式管理訊息實體。 此工具提供進階的功能，例如匯入/匯出功能和測試訊息實體及轉送服務的能力。 |1.8 |Microsoft Azure 服務匯流排；Service Bus for Windows Server |
| [授權：SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |這個範例會示範如何在 Microsoft Azure Active Directory 存取控制中建立和管理服務身分識別 (也稱為存取控制服務或 ACS) 以搭配服務匯流排使用。 |N/A |Microsoft Azure 服務匯流排 |

## <a name="next-steps"></a>後續步驟
請參閱下列主題以取得服務匯流排的概念性概觀。

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [服務匯流排架構](service-bus-architecture.md)
* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)


