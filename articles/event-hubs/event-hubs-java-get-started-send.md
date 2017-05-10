---
title: "使用 Java 將事件傳送至 Azure 事件中樞 | Microsoft Docs"
description: "開始使用 Java 傳送事件至事件中樞"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: jotaub;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 45776b0920f65ae9749b00978656bcefa2bf01a8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/04/2017


---
# <a name="send-events-to-azure-event-hubs-using-java"></a>使用 Java 將事件傳送至 Azure 事件中樞

## <a name="introduction"></a>簡介
事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱 [事件中樞概觀][Event Hubs overview]。

本教學課程也會示範如何使用以 Java 撰寫的主控台應用程式，將事件傳送到事件中樞。 若要使用 Java Event Processor Host 程式庫接收事件，請參閱[此文章](event-hubs-java-get-started-receive-eph.md)，或按一下左側目錄中適當的接收語言。

若要完成本教學課程，您需要下列項目：

* Java 開發環境。 在本教學課程中，我們將採用 [Eclipse](https://www.eclipse.org/)。
* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。 如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## <a name="send-messages-to-event-hubs"></a>將訊息傳送至事件中心
適用於事件中樞的 Java 用戶端程式庫可以在來自 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)的 Maven 專案中使用，而且可在您的 Maven 專案檔內使用下列相依性宣告來參考：    

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

對於不同類型的組建環境，您可以明確地從 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)或 [GitHub 上的版本發佈點](https://github.com/Azure/azure-event-hubs/releases)取得最新發行的 JAR 檔案。  

如果是簡單事件發行者，請針對事件中樞用戶端類別匯入 *com.microsoft.azure.eventhubs* 套件，以及針對公用程式類別匯入 *com.microsoft.azure.servicebus* 套件，例如，與 Azure 服務匯流排訊息用戶端共用的常見例外狀況。 

針對下列範例，在您最喜愛的 Java 開發環境中，先為主控台/殼層應用程式建立新的 Maven 專案。 此類別將稱為 `Send`。     

```Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

使用您建立事件中樞時所使用的值，來取代命名空間和事件中樞名稱。

```Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

接著將字串轉換為它的 utf-8 位元組編碼方式，藉以建立單一事件。 然後我們會從連接字串建立新的事件中樞用戶端執行個體，然後傳送訊息。   

```Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [使用 EventProcessorHost 接收事件](event-hubs-java-get-started-receive-eph.md)
* [事件中樞概觀][Event Hubs overview]
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
