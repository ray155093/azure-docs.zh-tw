---
title: 開發人員指南 - 了解裝置對應項 | Microsoft Docs
description: Azure IoT 中樞開發人員指南 - 使用裝置對應項同步處理 IoT 中樞與裝置之間的狀態和組態資料
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda

---
# <a name="understand-device-twins---preview"></a>了解裝置對應項 - 預覽
## <a name="overview"></a>概觀
「裝置對應項」是存放裝置狀態資訊 (中繼資料、組態和條件) 的 JSON 文件。 IoT 中樞會為連線到 IoT 中樞的每個裝置保存裝置對應項。 本文將說明︰

* 裝置對應項的結構︰標籤、所需屬性和報告屬性，以及
* 裝置應用程式和後端可以對裝置對應項執行的作業。

> [!NOTE]
> 目前，裝置對應項只能從使用 MQTT 通訊協定連線至 IoT 中樞的裝置存取。 請參閱 [MQTT 支援][lnk-devguide-mqtt] 文章，以取得如何將現有裝置應用程式轉換為使用 MQTT 的指示。
> 
> 

### <a name="when-to-use"></a>使用時機
使用裝置對應項可以：

* 在雲端儲存裝置特定的中繼資料，例如販賣機的部署位置。
* 報告裝置應用程式 (例如透過行動電話網路或 wifi 連線的裝置) 的目前狀態資訊，例如可用功能和狀況。
* 在裝置應用程式和後端之間同步處理長時間執行之工作流程的狀態，例如指定要安裝之新韌體版本的後端，以及報告更新處理程序各個階段的裝置應用程式。
* 查詢裝置的中繼資料、組態或狀態。

請為一連串的時間戳記事件 (例如感應器資料或警示的時間序列) 使用[裝置到雲端的訊息][lnk-d2c]。 請為裝置的互動式控制 (例如開啟風扇) 使用[雲端到裝置方法][lnk-methods]。

## <a name="device-twins"></a>裝置對應項
裝置對應項會儲存裝置相關資訊，以供︰

* 裝置和後端用來同步處理裝置的狀況和組態。
* 應用程式後端用來查詢和鎖定長時間執行的作業。

裝置對應項的生命週期會連結至對應的[裝置身分識別][lnk-identity]。 在 IoT 中樞建立或刪除新的裝置身分識別時會隱含地建立和刪除對應項。

裝置的兩個是 JSON 文件，其中含有︰

* **標籤**。 後端所讀取和寫入的 JSON 文件。 裝置應用程式看不到標籤。
* **所需屬性**。 與報告屬性搭配使用以便同步處理裝置的組態或狀況。 所需屬性只能由應用程式後端設定，並可供裝置應用程式讀取。 所需屬性有所變更時，裝置應用程式也可以即時收到通知。
* **報告屬性**。 與所需屬性搭配使用以便同步處理裝置的組態或狀況。 報告屬性只能由裝置應用程式設定，並可供應用程式後端讀取和查詢。

此外，裝置對應項的根目錄包含來自對應身分識別的唯讀屬性，如[裝置身分識別登錄][lnk-identity]中所含。

![][img-twin]

以下是裝置對應項的 JSON 文件範例︰

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

在根物件，都是系統屬性，並且是 `tags` 以及 `reported` 和 `desired properties` 的容器物件。 `properties` 容器包含一些唯讀元素 (`$metadata`、`$etag` 和 `$version`)，其說明分別位於[對應項中繼資料][lnk-twin-metadata]和[開放式並行存取][lnk-concurrency]章節。

### <a name="reported-property-example"></a>報告屬性範例
在上述範例中，裝置對應項包含由裝置應用程式所報告的 `batteryLevel` 屬性。 此屬性可讓您根據最後一次報告的電池電量對裝置進行查詢和操作。 另一個範例會具有裝置應用程式報告功能或連線選項。

請注意報告屬性是如何簡化後端對屬性的最後一個已知值感興趣的案例。 如果後端需要以一連串時間戳記事件 (例如時間序列) 的形式處理裝置遙測，請使用[裝置到雲端的訊息][lnk-d2c]。

### <a name="desired-configuration-example"></a>所需組態範例
在上述範例中，後端和裝置應用程式會使用 `telemetryConfig` 所需屬性和報告屬性來同步處理此裝置的遙測組態。 例如：

1. 應用程式後端會以所需組態值設定所需屬性。 以下是含有所需屬性的文件部分︰
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. 裝置應用程式會在已連線或第一次重新連線時，立即收到變更通知。 裝置應用程式接著會報告更新的組態 (或使用 `status` 屬性的錯誤狀況)。 以下是報告屬性的部分︰
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. 透過[查詢][lnk-query]對應項，應用程式後端可以跨裝置持續追蹤組態作業的結果。

> [!NOTE]
> 上述程式碼片段舉例說明了可能用來編碼裝置組態和其狀態的方式，為了方便閱讀，其內容已經過最佳化。 IoT 中樞不會對裝置對應項中的所需屬性和報告屬性強制實行特定結構描述。
> 
> 

在許多情況下，裝置對應項可用來同步處理長時間執行的作業，例如韌體更新。 如需如何使用屬性來跨裝置同步處理及追蹤長時間執行的作業，請參閱[使用所需屬性來設定裝置][lnk-twin-properties]。

## <a name="back-end-operations"></a>後端作業
後端會使用下列不可部分完成的作業 (透過 HTTP 公開) 來操作對應項︰

1. **依識別碼擷取對應項**。 此作業會傳回對應項文件的內容，包括標籤以及所需屬性、報告屬性和系統屬性。
2. **部分更新對應項**。 此作業可讓後端部分更新對應項的標籤或所需屬性。 部分更新會以 JSON 文件的形式來表示，以新增或更新上述任何屬性。 設定為 `null` 的屬性會遭到移除。 例如，下列程式碼會以 `{"newProperty": "newValue"}` 值建立新的所需屬性、以 `"otherNewValue"` 覆寫 `existingProperty` 的現有值，並完全移除 `otherOldProperty`。 其他現有的所需屬性或標籤不會發生任何變更︰
   
        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }
3. **取代所需屬性**。 此作業可讓後端完全覆寫所有現有的所需屬性，並以新的 JSON 文件取代 `properties/desired`。
4. **取代標籤**。 與取代所需屬性類似，此作業可讓後端完全覆寫所有現有標籤，並以新的 JSON 文件取代 `tags`。

所有上述作業皆支援[開放式並行存取][lnk-concurrency]，而且需要 **ServiceConnect** 權限，如[安全性][lnk-security]所定義。

除了這些作業外，後端還可以使用類似 SQL 的[查詢語言][lnk-query]查詢對應項，並使用[作業][lnk-jobs]對大量的對應項組執行作業。

## <a name="device-operations"></a>裝置作業
裝置應用程式會使用下列不可部分完成的作業來操作對應項︰

1. **擷取對應項**。 此作業會針對目前連線的裝置傳回對應項文件的內容 (包括標籤以及所需屬性、報告屬性和系統屬性)。
2. **部分更新的報告屬性**。 此作業可針對目前連線裝置的報告屬性進行部分更新。 這會使用和面臨要對所需屬性進行部分更新之後端相同的 JSON 更新格式。
3. **觀察所需屬性**。 目前連線的裝置可以選擇在所需屬性進行更新時立即收到通知。 裝置會收到由後端執行的相同形式更新 (部分或完整取代)。

上述所有作業都需要 **DeviceConnect** 權限，如[安全性][lnk-security]一文所定義。

[Azure IoT 裝置 SDK][lnk-sdks] 可讓您透過許多語言和平台輕鬆使用上述作業。 如需 IoT 中樞之所需屬性同步處理基本類型的詳細資訊，請參閱[裝置重新連線流程][lnk-reconnection]。

> [!NOTE]
> 目前，裝置對應項只能從使用 MQTT 通訊協定連線至 IoT 中樞的裝置存取。
> 
> 

## <a name="reference"></a>參考
### <a name="tags-and-properties-format"></a>標籤和屬性格式
標籤、所需屬性和報告屬性是具有下列限制的 JSON 物件︰

* JSON 物件中的所有索引鍵是區分大小寫的 128 字元 UNICODE 字串。 允許的字元會排除 UNICODE 控制字元 (區段 C0 和 C1)，以及 `'.'`、`' '` 和 `'$'`。
* JSON 物件中的所有值可以屬於下列 JSON 類型︰布林值、數字、字串、物件。 不允許使用陣列。

### <a name="twin-size"></a>對應項大小
IoT 中樞會對 `tags`、`properties/desired` 和 `properties/reported` 的值 (排除唯讀元素) 強制執行 8 KB 大小的限制。
大小的計算方式是計算所有字元的數量，並排除出現在字串常數之外的 UNICODE 控制字元 (區段 C0 和 C1) 和空格 `' '`。
IoT 中樞會拒絕 (並出現錯誤) 將會讓這些文件的大小增加到超過限制的所有作業。

### <a name="twin-metadata"></a>對應項中繼資料
IoT 中樞會為所需屬性和報告屬性的每個 JSON 物件保有上次更新的時間戳記。 時間戳記採用 UTC 格式，並以 [ISO8601] 格式 `YYYY-MM-DDTHH:MM:SS.mmmZ` 進行編碼。
例如：

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

此資訊會保留在每個層級 (而不只是 JSON 結構的分葉)，以保留可移除物件索引鍵的更新。

### <a name="optimistic-concurrency"></a>開放式並行存取
標籤、所需屬性和報告屬性全都支援開放式並行存取。
依據 [RFC7232]，標籤會有一個 Etag 來表示該標籤的 JSON 表示法。 您可以在條件式更新作業中從後端使用此標籤來確保一致性。

所需屬性和報告屬性沒有 Etag，但是有一定會遞增的 `$version` 值。 與 Etag 類似，更新端 (例如報告屬性的裝置應用程式或所需屬性的後端) 可以使用版本來強制保有一致的更新。

當觀察端代理程式 (例如，觀察所需屬性的裝置應用程式) 必須協調擷取作業結果與更新通知之間的競爭情況時，版本也相當有用。 [裝置重新連線流程][lnk-reconnection]一節會提供詳細資訊。

### <a name="device-reconnection-flow"></a>裝置重新連線流程
IoT 中樞不會保留已中斷連線之裝置的所需屬性更新通知。 因此，連線的裝置必須擷取完整的所需屬性文件，並訂閱更新通知。 由於更新通知和完整擷取之間可能會有競爭情況，因此必須確保下列流程︰

1. 裝置應用程式連線到 IoT 中樞。
2. 裝置應用程式訂閱所需屬性更新通知。
3. 裝置應用程式擷取所需屬性的完整文件。

裝置應用程式可以忽略 `$version` 小於或等於完整擷取文件版本的所有通知。 IoT 中樞保證版本一定會遞增，因此這是可行的。

> [!NOTE]
> [Azure IoT 裝置 SDK][lnk-sdks] 中已實作此邏輯。 只有當裝置應用程式無法使用任何 Azure IoT 裝置 SDK，因而必須直接為 MQTT 介面編寫程式時，此說明才有用。
> 
> 

### <a name="additional-reference-material"></a>其他參考資料
開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints] 說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。
* [節流和配額][lnk-quotas] 說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。
* [IoT 中樞裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，您可以在開發裝置與服務應用程式 (與 IoT 中樞互動) 時使用。
* [twins、方法和作業的查詢語言][lnk-query] 說明查詢語言，可用來從 IoT 中樞擷取關於裝置 twins、方法和作業的資訊。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt] 針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
現在您已了解裝置對應項，接下來您可能會對下列開發人員指南主題感興趣︰

* [叫用裝置上的直接方法][lnk-methods]
* [排程多個裝置上的作業][lnk-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [如何使用裝置對應項][lnk-twin-tutorial]
* [如何使用對應項屬性][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png


<!--HONumber=Oct16_HO2-->


