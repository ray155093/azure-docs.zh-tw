---
title: "了解 Azure IoT 中樞身分識別登錄 | Microsoft Docs"
description: "開發人員指南 - 說明 IoT 中樞身分識別登錄和如何用來管理裝置。 包含大量匯入和匯出裝置識別身分的相關資訊。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 75a2fa16a7e33cf85746538e120ca90a389b05c5
ms.lasthandoff: 03/24/2017


---
# <a name="understand-identity-registry-in-your-iot-hub"></a>了解 IoT 中樞的身分識別登錄

## <a name="overview"></a>概觀

每個 IoT 中樞都有身分識別登錄，可儲存允許連線至 IoT 中樞之裝置的相關資訊。 若要讓裝置可以連線到 IoT 中樞，IoT 中樞的身分識別登錄中必須先有該裝置的項目。 裝置也必須根據身分識別登錄中儲存的認證，向 IoT 中樞進行驗證。

總括來說，身分識別登錄是支援 REST 的裝置身分識別資源集合。 當您在身分識別登錄中新增項目時，IoT 中樞會在服務 (例如，包含傳遞中雲端到裝置訊息的佇列) 中建立一組每一裝置資源。

### <a name="when-to-use"></a>使用時機

當您需要佈建連線到 IoT 中樞的裝置，以及當您需要控制每一裝置對中樞之裝置面向端點的存取權時，請使用身分識別登錄。

> [!NOTE]
> 身分識別登錄不包含任何應用程式特有中繼資料。

## <a name="identity-registry-operations"></a>身分識別登錄作業

IoT 中樞身分識別登錄會公開下列作業︰

* 建立裝置身分識別
* 更新裝置身分識別
* 依照 ID 擷取裝置身分識別別
* 刪除裝置身分識別
* 列出多達 1000 個識別
* 將所有身分識別匯出至 Azure Blob 儲存體
* 從 Azure Blob 儲存體匯入身分識別

上述所有作業均可使用 [RFC7232][lnk-rfc7232] 中指定的開放式並行存取。

> [!IMPORTANT]
> 如果要擷取 IoT 中樞的身分識別登錄中的所有身分識別，唯一方法是使用[匯出][lnk-export]功能。

IoT 中樞身分識別登錄：

* 不包含任何應用程式中繼資料。
* 可以將 **deviceId** 做為索引鍵來存取，就像字典一樣。
* 不支援表達式查詢。

IoT 方案通常具有不同的方案專屬存放區，其中包含應用程式特定的中繼資料。 例如，智慧建置方案中的解決方案專用存放區會記錄部署溫度感應器的空間。

> [!IMPORTANT]
> 只能將身分識別登錄用於裝置管理和佈建作業。 執行階段的高輸送量作業不應該仰賴在身分識別登錄中執行作業。 例如，在傳送命令前先檢查裝置的連線狀態就不是支援的模式。 請務必檢查身分識別登錄的[節流速率][lnk-quotas]以及[裝置活動訊號][lnk-guidance-heartbeat]模式。

## <a name="disable-devices"></a>停用裝置

您可以在身分識別登錄中更新身分識別的 [狀態] 屬性來停用裝置。 您通常會在兩種情況下使用此屬性：

* 在佈建協調程序期間。 如需詳細資訊，請參閱[裝置佈建][lnk-guidance-provisioning]。
* 如果因為任何原因，您認為裝置遭到入侵，或變成未經授權。

## <a name="import-and-export-device-identities"></a>匯入和匯出裝置身分識別

您可以使用 [IoT 中樞資源提供者端點][lnk-endpoints]上的非同步作業，從 IoT 中樞的身分識別登錄大量匯出裝置身分識別。 匯出是長時間執行的作業，其使用客戶提供的 Blob 容器來儲存讀取自身分識別登錄的裝置身分識別資料。

您可以使用 [IoT 中樞資源提供者端點][lnk-endpoints]上的非同步作業，將裝置身分識別大量匯入 IoT 中樞的身分識別登錄。 匯入是長時間執行的作業，其使用客戶提供的 Blob 容器中的資料，將裝置身分識別資料寫入至身分識別登錄。

* 如須匯入和匯出 API 的詳細資訊，請參閱 [IoT 中樞資源提供者 REST API][lnk-resource-provider-apis]。
* 若要深入了解如何執行匯入和匯出作業，請參閱[大量管理 IoT 中樞的裝置身分識別][lnk-bulk-identity]。

## <a name="device-provisioning"></a>裝置佈建

給定的 IoT 解決方案儲存的裝置資料取決於該解決方案的特定需求。 但是解決方案至少必須儲存裝置身分識別和驗證金鑰。 Azure IoT 中樞包含身分識別登錄，其可以儲存每個裝置的值，例如識別碼、驗證金鑰和狀態碼。 解決方案可以使用其他 Azure 服務 (例如 Azure 表格儲存體、Azure Blob 儲存體或 Azure DocumentDB) 來儲存任何其他裝置資料。

*裝置佈建* 是將初始裝置資料加入至解決方案中存放區的程序。 若要讓新裝置能夠連接到您的中樞，必須將裝置識別碼和金鑰新增至「IoT 中樞」身分識別登錄。 做為佈建程序的一部分，您可能需要初始化其他解決方案存放區中的裝置特定資料。

## <a name="device-heartbeat"></a>裝置活動訊號

IoT 中樞身分識別登錄包含稱為 **connectionState** 的欄位。 請只在進行開發和偵錯時才使用 **connectionState**。 IoT 解決方案不應該在執行階段查詢該欄位 (例如，檢查裝置是否已連接，以決定是要傳送雲端到裝置訊息，還是 SMS)。

如果 IoT 解決方案需要知道是否已連接裝置 (不論是在執行階段，還是需要比 **connectionState** 屬性所提供的資訊更精確時)，您應該實作「活動訊號模式」。

在活動訊號模式中，裝置每隔固定時間就會至少傳送一次裝置到雲端的訊息 (例如，每小時至少一次)。 因此，即使裝置沒有任何要傳送的資料，它仍會傳送空的裝置到雲端的訊息 (通常具有可識別它是活動訊號的屬性)。 此解決方案會在服務端保有一份對應，其中有針對每個裝置收到的最後一次活動訊號。 如果裝置沒有在預期時間內收到活動訊號訊息，此解決方案就會假設該裝置發生問題。

更複雜的實作可以包含來自[作業監視][lnk-devguide-opmon]的資訊，以便識別嘗試連接或通訊但失敗的裝置。 當您實作活動訊號模式時，請務必檢查 [IoT 中樞配額與節流][lnk-quotas]。

> [!NOTE]
> 如果 IoT 解決方案只需要裝置連線狀態來判斷是否要傳送雲端到裝置訊息，而且訊息不會廣播到大量的裝置組合，則要考慮的較簡單模式是使用較短的到期時間。 此模式所達到的效果與使用活動訊號模式來維護裝置連線狀態登錄相同，但更有效率。 它也能夠藉由要求訊息收條，收到 IoT 中樞的通知，告知哪些裝置能夠收到訊息，而哪些裝置不在線上或故障。

## <a name="reference-topics"></a>參考主題：

下列參考主題會提供您關於身分識別登錄的詳細資訊。

## <a name="device-identity-properties"></a>裝置身分識別屬性

裝置身分識別會以具有下列屬性的 JSON 文件表示：

| 屬性 | 選項 | 說明 |
| --- | --- | --- |
| deviceId |必要，只能讀取更新 |區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| generationId |必要，唯讀 |IoT 中樞產生的區分大小寫字串，最長為 128 個字元。 此值可用來在刪除並重建裝置時，區分具有相同 **deviceId** 的裝置。 |
| etag |必要，唯讀 |依據 [RFC7232][lnk-rfc7232]，此字串代表裝置身分識別的弱式 ETag。 |
| auth |選用 |包含驗證資訊和安全性資料的複合物件。 |
| auth.symkey |選用 |包含主要和次要金鑰 (以 base64 格式儲存) 的複合物件。 |
| status |必要 |存取指示器。 可以是 [已啟用] 或 [已停用]。 如果為 [已啟用] ，則允許連接裝置。 如果為 [已停用] ，此裝置無法存取任何裝置面向的端點。 |
| statusReason |選用 |長度為 128 個字元的字串，用來儲存裝置身分識別狀態的原因。 允許所有 UTF-8 字元。 |
| statusUpdateTime |唯讀 |暫時指示器，顯示上次狀態更新的日期和時間。 |
| connectionState |唯讀 |指出連線狀態的欄位︰**已連線**或**已中斷連線**。 這個欄位代表裝置連線狀態的 IoT 中樞檢視。 **重要事項**：此欄位只應用於開發/偵錯用途。 只有針對使用 MQTT 或 AMQP 的裝置才會更新連線狀態。 此外，它是以通訊協定層級的偵測 (MQTT 偵測或 AMQP 偵測) 為基礎，而且最多只能有 5 分鐘的延遲。 基於這些理由，其中可能會有誤判的情形，例如將裝置回報為已連線，但卻已中斷連線。 |
| connectionStateUpdatedTime |唯讀 |暫時指示器，顯示上次更新連線狀態的日期和時間。 |
| lastActivityTime |唯讀 |暫時指示器，顯示裝置上次連接、接收或傳送訊息的日期和時間。 |

> [!NOTE]
> 連線狀態只能代表連線狀態的 IoT 中樞檢視。 根據網路狀況和組態而定，可能會延遲此狀態的更新。

## <a name="additional-reference-material"></a>其他參考資料

IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints]說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。
* [節流和配額][lnk-quotas]說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [Azure IoT 裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。
* [裝置對應項和作業的 IoT 中樞查詢語言][lnk-query]說明的 IoT 中樞查詢語言可用來從 IoT 中樞擷取有關裝置對應項和作業的資訊。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt]針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已了解如何使用 IoT 中樞身分識別登錄，接下來您可能對下列 IoT 中樞開發人員指南主題感興趣︰

* [控制 IoT 中樞的存取權][lnk-devguide-security]
* [使用裝置對應項同步處理狀態和組態][lnk-devguide-device-twins]
* [在裝置上叫用直接方法][lnk-devguide-directmethods]
* [排程多個裝置上的作業][lnk-devguide-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [開始使用 Azure IoT 中樞][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md

