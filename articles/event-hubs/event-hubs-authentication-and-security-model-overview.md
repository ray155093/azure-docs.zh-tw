---
title: "Azure 事件中樞驗證和安全性模型概觀 | Microsoft Docs"
description: "事件中樞驗證和安全性模型概觀。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2016
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: 3f20a19c212c082aa766cc2bd67e938aaabf715e


---
# <a name="event-hubs-authentication-and-security-model-overview"></a>事件中樞驗證和安全性模型概觀
Azure 事件中樞安全性模型符合下列需求：

* 唯有提供有效憑證的裝置能將資料傳送到事件中樞。
* 裝置無法模擬另一個裝置。
* 可封鎖惡意裝置，避免該裝置將資料傳送到事件中樞。

## <a name="device-authentication"></a>裝置驗證
事件中樞安全性模型是以 [共用存取簽章 (SAS)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) 權杖和「事件發行者」的組合為基礎。 事件發佈者能定義事件中樞的虛擬端點。 發佈者只能用來將訊息傳送到事件中樞。 您無法接收來自發佈者的訊息。

一般而言，事件中樞會針對每個裝置採用一個發佈者。 系統會將所有傳送到事件中樞之任何發佈行者的訊息排入該事件中樞內的佇列。 發行者會啟用更細緻的存取控制和節流。

系統會為每個裝置指派一個唯一權杖，該權杖會上傳至裝置。 權杖的產生機制讓每個唯一權杖都能授與相異唯一發佈者的存取權限。 擁有權杖的裝置只能傳送到一個發佈者，無法再傳送到其他發佈者。 如果多個裝置共用同一個權杖，這些裝置會共用一個發佈者。

您可以為裝置配備授與事件中樞直接存取權限的權杖，不過我們不建議這樣做。 任何擁有此權杖的裝置都可以將訊息直接傳送到該事件中樞。 這類裝置將不受節流所約束。 此外，您無法將這類裝置加入黑名單，禁止其傳送到該事件中樞。

所有權杖都經過 SAS 金鑰簽署。 一般而言，所有權杖都會經過同一個金鑰簽署。 裝置無法感知金鑰，如此能避免裝置製造權杖。

### <a name="create-the-sas-key"></a>建立 SAS 金鑰
建立 Azure 事件中樞命名空間時，此服務會產生名為 **RootManageSharedAccessKey** 的 256 位元 SAS 金鑰。 這個金鑰能授與命名空間的傳送、聆聽及管理權限。 您可以建立額外的金鑰。 建議您產生授與特定事件中樞之傳送權限的金鑰。 在本主題的其餘部分，假設您將此金鑰命名為 **EventHubSendKey**。

以下範例會在建立事件中樞時建立僅限傳送的金鑰：

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>產生權杖
您可以使用 SAS 金鑰來產生權杖。 您只能為每個裝置產生一個權杖。 您可以使用下列方法來產生權杖。 所有權杖都是以 **EventHubSendKey** 金鑰產生。 每個權杖均有一個指派的唯一 URI。

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

在呼叫這個方法時，您應該將 URI 指定為 `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`。 此 URI 對所有權杖來說是完全相同的，但 `PUBLISHER_NAME`除外，每個權杖的該項目應該是不同的。 在理想的情況下， `PUBLISHER_NAME` 代表收到該權杖之裝置的識別碼。

這個方法會產生具有下列結構的權杖：

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

以秒數為單位指定的權杖到期 (從 1970 年 1 月 1 日 開始)。 以下是權杖的範例：

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

一般而言，權杖的週期與裝置週期相近或大於裝置週期。 如果裝置有能力取得新權杖，就可以使用週期較短的權杖。

### <a name="devices-sending-data"></a>傳送資料的裝置
一旦建立權杖之後，每個裝置即會佈建自己的唯一權杖。

當裝置將資料傳送到事件中樞時，裝置會為自己的權杖加上傳送要求標記。 若要預防攻擊者竊聽及竊取權杖，裝置和事件中樞之間的通訊必須透過加密通道。

### <a name="blacklisting-devices"></a>將裝置列入黑名單
如果權杖遭攻擊者竊取，攻擊者便可以模擬權杖遭竊的裝置。 將裝置列入黑名單能使裝置無法使用，直到裝置擁有使用不同發佈者的權杖為止。

## <a name="authentication-of-back-end-applications"></a>後端應用程式的驗證
為了驗證取用裝置產生之資料的後端應用程式，事件中樞會採用與服務匯流排主題所用之模型相似的安全性模型。 事件中樞取用者群組等同於服務匯流排主題的訂用帳戶。 如果建立取用者群組的要求伴隨著授與事件中樞或事件中樞所屬之命名空間管理權限的權杖，用戶端便可以建立取用者群組。 如果接收要求伴隨著授與取用者群組、事件中樞或事件中樞所屬之命名空間接收權限的權杖，用戶端便可以取用來自取用者群組的資料。

目前版本的服務匯流排不支援個別訂用帳戶的 SAS 規則。 相同情況亦適用於事件中樞取用者群組。 我們會在日後將這兩項功能加入 SAS 支援。

由於個別取用者群組的 SAS 驗證不存在，因此您可以使用 SAS 金鑰來保護所有使用相同金鑰的取用者群組。 這個方法能讓應用程式取用來自事件中樞之任何取用者群組的資料。

## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞，請造訪下列主題：

* [事件中樞概觀]
* [SAS 概觀]
* [使用事件中樞的完整範例應用程式]

[事件中樞概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[SAS 概觀]: ../service-bus-messaging/service-bus-sas-overview.md




<!--HONumber=Dec16_HO1-->


