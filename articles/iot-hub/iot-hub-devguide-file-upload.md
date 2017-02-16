---
title: "了解 Azure IoT 中樞檔案上傳 | Microsoft Docs"
description: "開發人員指南 - 使用 IoT 中樞的檔案上傳功能，管理將檔案從裝置上傳至 Azure 儲存體 blob 容器。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9ded95283b52f0fc21ca5b99df8e72e1e152fe1c
ms.openlocfilehash: c56a568fa003ec45e92279e070e6309763071827


---
# <a name="file-uploads-with-iot-hub"></a>使用 IoT 中樞上傳檔案
## <a name="overview"></a>概觀
如 [IoT 中樞端點][lnk-endpoints]一文所詳述，裝置可以藉由透過面向裝置的端點 (**/devices/{deviceId}/files**) 傳送通知，來起始檔案上傳。  當裝置通知 IoT 中樞已完成上傳時，IoT 中樞會產生檔案上傳通知，您可以透過面向服務的端點 (**/messages/servicebound/filenotifications**) 利用訊息收到。

不是透過 IoT 中樞本身的代理訊息，IoT 中樞會做為相關聯 Azure 儲存體帳戶的發送器。 裝置會向 IoT 中樞要求儲存體權杖，這是裝置想要上傳的檔案的特定權杖。 裝置會使用 SAS URI，將檔案上傳至儲存體，上傳完成時，裝置會將完成的通知傳送到 IoT 中樞。 IoT 中樞會確認檔案已上傳，然後將檔案上傳通知新增至新服務面向檔案通知訊息端點。

將檔案從裝置上傳到 IoT 中樞之前，您必須先對中樞[關聯 Azure 儲存體][lnk-associate-storage]帳戶以設定中樞。

接著，您的裝置就可以[初始化上傳][lnk-initialize]，然後在上傳完成時[通知 IoT 中樞][lnk-notify]。 (選擇性) 當裝置將上傳已完成的狀態通知 IoT 中樞時，服務就會產生[通知訊息][lnk-service-notification]。

### <a name="when-to-use"></a>使用時機
使用檔案上傳來傳送間歇性連線裝置所上傳或為了節省頻寬而壓縮的媒體檔案和大型遙測批次。

如果不確定要使用報告屬性、裝置對雲端訊息或檔案上傳，請參閱[裝置到雲端通訊指引][lnk-d2c-guidance]。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>讓 Azure 儲存體帳戶與 IoT 中樞產生關聯
若要使用檔案上傳功能，您必須先將 Azure 儲存體帳戶連結至 IoT 中樞。 您可以透過 [Azure 入口網站][lnk-management-portal]完成此工作，或透過 [IoT 中樞資源提供者 REST API][lnk-resource-provider-apis] 以程式設計方式完成此工作。 一旦您將 Azure 儲存體帳戶與 IoT 中樞產生關聯，服務會在裝置起始檔案上傳要求時，將 SAS URI 傳回至裝置。

> [!NOTE]
> [Azure IoT SDK][lnk-sdks] 會自動處理擷取 SAS URI、上傳檔案，並且通知 IoT 中樞已完成上傳。
> 
> 

## <a name="initialize-a-file-upload"></a>初始化檔案上傳
IoT 中樞擁有專供裝置用來要求儲存體 SAS URI 以便上傳檔案的端點。 裝置會起始檔案上傳程序，方法是將具有下列 JSON 主體的 POST 傳送至 IoT 中樞的 `{iot hub}.azure-devices.net/devices/{deviceId}/files`：

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

「IoT 中樞」會傳回下列資料，供裝置用來上傳檔案︰

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>已被取代︰使用 GET 初始化檔案上傳
> [!NOTE]
> 本節說明如何從 IoT 中樞接收 SAS URI 的功能，但此功能已被取代。 您應該使用先前所述的 POST 方法。
> 
> 

IoT 中樞有兩個 REST 端點可以支援檔案上傳，一個用來取得儲存體的 SAS URI，另一個用來通知 IoT 中樞已完成上傳。 裝置會起始檔案上傳程序，方法是將 GET 傳送至 IoT 中樞的 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`。 IoT 中樞會傳回要上傳之檔案的特定 SAS URI，以及上傳完成後要使用的相互關聯識別碼。

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>通知 IoT 中樞已完成檔案上傳
裝置會負責使用 Azure 儲存體 SDK 將檔案上傳至儲存體。 上傳完成後，裝置會將具有下列 JSON 主體的 POST 傳送至 IoT 中樞的 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications`︰

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 的值是一個代表是否已成功上傳檔案的布林值。 `statusCode` 的狀態碼是檔案上傳至儲存體的狀態，而且 `statusDescription` 會對應至 `statusCode`。

## <a name="reference-topics"></a>參考主題：
下列參考主題會提供您關於從裝置上傳檔案的詳細資訊。

## <a name="file-upload-notifications"></a>檔案上傳通知
當裝置上傳檔案並通知 IoT 中樞上傳完成時，服務會選擇性地產生通知訊息，其中包含檔案的名稱和儲存體位置。

如[端點][lnk-endpoints]中所述，IoT 中樞會透過面向服務的端點 (**/messages/servicebound/fileuploadnotifications**) 利用訊息來傳遞檔案上傳通知。 檔案上傳通知的接收語意與雲端到裝置訊息的接收語意相同，並且具有相同的[訊息生命週期][lnk-lifecycle]。 從檔案上傳通知端點擷取的每則訊息是具有下列屬性的 JSON 記錄：

| 屬性 | 說明 |
| --- | --- |
| EnqueuedTimeUtc |指出通知建立時間的時間戳記。 |
| deviceId |**DeviceId** 。 |
| BlobUri |上傳檔案的 URI。 |
| BlobName |上傳檔案的名稱。 |
| LastUpdatedTime |指出上次更新檔案的時間戳記。 |
| BlobSizeInBytes |上傳檔案的大小。 |

**範例**。 此範例顯示檔案上傳通知訊息的本文。

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>檔案上傳通知組態選項
每個 IoT 中樞都會針對檔案上傳通知公開下列組態選項：

| 屬性 | 說明 | 範圍和預設值 |
| --- | --- | --- |
| **enableFileUploadNotifications** |控制是否將檔案上傳通知寫入檔案通知端點。 |布林 預設值：True。 |
| **fileNotifications.ttlAsIso8601** |檔案上傳通知的預設 TTL。 |ISO_8601 間隔高達 48H (最小為 1 分鐘)。 預設值︰1 小時。 |
| **fileNotifications.lockDuration** |檔案上傳通知佇列的鎖定持續時間。 |5 到 300 秒 (最小值 5 秒)。 預設值：60 秒。 |
| **fileNotifications.maxDeliveryCount** |檔案上傳通知佇列的傳遞計數上限。 |1 到 100。 預設值 = 100。 |

## <a name="additional-reference-material"></a>其他參考資料
IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints]說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。
* [節流和配額][lnk-quotas]說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [Azure IoT 裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。
* [裝置對應項和作業的 IoT 中樞查詢語言][lnk-query]說明的 IoT 中樞查詢語言可用來從 IoT 中樞擷取有關裝置對應項和作業的資訊。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt]針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
現在您已了解如何使用 IoT 中樞從裝置上傳檔案，接下來您可能對下列 IoT 中樞開發人員指南主題感興趣︰

* [在 IoT 中樞管理裝置身分識別][lnk-devguide-identities]
* [控制 IoT 中樞的存取權][lnk-devguide-security]
* [使用裝置對應項同步處理狀態和組態][lnk-devguide-device-twins]
* [在裝置上叫用直接方法][lnk-devguide-directmethods]
* [排程多個裝置上的作業][lnk-devguide-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [如何使用 IoT 中樞從裝置將檔案上傳至雲端][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md



<!--HONumber=Jan17_HO1-->


