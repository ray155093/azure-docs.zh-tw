---
title: IoT 中樞裝置管理對應項查詢 | Microsoft Docs
description: Azure IoT 中樞裝置管理教學課程說明如何使用查詢來尋找裝置對應項。
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# 教學課程：如何使用查詢與 node.js 找出裝置對應項 (預覽)
[!INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Azure IoT 裝置管理可讓您使用查詢找出裝置對應項 (實體裝置的服務呈現)。您可以根據裝置對應項中的裝置屬性、服務屬性或標記來進行查詢。您可以使用標記和屬性進行查詢︰

* 若要使用標記來查詢裝置對應項，您可以傳遞字串陣列，而且查詢會傳回加上所有這些字串的裝置集合。
* 若要使用服務屬性或裝置屬性來查詢裝置對應項，您可以使用 JSON 查詢運算式。

如需裝置對應項和查詢的詳細資訊，請參閱 [Azure IoT 中樞裝置管理概觀][lnk-dm-overview]。

## 執行裝置查詢範例
下列範例延伸了[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]教學課程的功能。從執行不同的模擬裝置開始，會使用查詢來尋找特定裝置。

### 必要條件
執行此範例之前，您必須已完成[開始使用 Azure IoT 中樞裝置管理][lnk-get-started]中的步驟。這表示您的模擬裝置必須為執行中。如果您之前已完成程序，請立即重新啟動您的模擬裝置。

### 啟動範例
若要啟動範例，您需要執行 `registry_queryDevices.js`。這將會執行幾個不同的查詢。遵循下列步驟來啟動範例：

1. 可讓模擬裝置至少執行 1 分鐘。這確保對應項中的實體裝置與裝置屬性同步。如需同步處理的詳細資訊，請參閱[教學課程︰如何使用裝置對應項][lnk-twin-tutorial]。
2. 從您複製 **azure-iot-sdks** 儲存機制的根資料夾，瀏覽至 **azure-iot-sdks/node/service/samples** 目錄。
3. 開啟 **registry\_queryDevices.js**，以您的 IoT 中樞連接字串取代預留位置。
4. 執行 `node registry_queryDevices.js`。

您應該會看到命令列視窗中的輸出，這個輸出使用標記、服務屬性和裝置屬性來顯示裝置物件的查詢結果。

## 查詢結構 (JSON)
使用 JSON 字串針對裝置屬性和服務屬性執行查詢，來代表查詢本身。JSON 字串包含 4 個部分。以下是每個部分的說明以及可序列化為正確 JSON 字串的 C# 物件。

* **專案**：運算式指定裝置物件中要包含在查詢結果集中的欄位 (相當於 SQL 中的 SELECT)：
  
    ```
    var projectQuery = {
        "project": {
            "all": false,
            "properties": [{
                "name": "CustomerId",
                "type": "service"
            }, {
                "name": "Weight",
                "type": "service"
            }]
        }
    }
    ```
* **篩選**：運算式限制查詢結果集中所包含的裝置物件 (相當於 SQL 中的 WHERE)：
  
    ```
    var filterQuery = {
      filter: {
        property: {
          name: "CustomerId",
          type: "service"
        },
        value: "123456",
        comparisonOperator: "eq",
        type: "comparison"
      },
      project: null,
      aggregate: null,
      sort: null
    };
    ```
* **彙總**：運算式決定如何分組查詢結果集 (相當於 SQL 中的 GROUPBY)：
  
    ```
    var aggregateQuery = {
    filter: null,
    project: null,
    aggregate: {
    keys: [
      {
        name: "CustomerId",
        type: "service"
      }
    ],
    properties: [
      {
        operator: "sum",
        property: {
          name: "Weight",
          type: "service"
        },
        columnName: "TotalWeight"
      }
    ]
    },
    sort: null
    };
    ```
* **排序**︰屬性應該用來排序查詢結果集的運算式定義 (相當於 SQL 中的 ORDER BY)。如果排序是 null，則預設會使用 **deviceID**：

```
  var sortQuery = {
  filter: null,
  project: null,
  aggregate: null,
  sort: [
      {
          order: "asc",
          property: {
              name: "QoS",
              type: "service"
          }
      }
  ]
  };
```

### 限制
公開預覽查詢實作有一些限制。

* 不會驗證查詢運算式。
* 查詢會區分大小寫。
* 使用查詢運算式依服務或裝置屬性進行查詢時，只會傳回 100 個裝置。[我們的查詢程式庫][lnk-query-samples]提供如何實作分頁的範例。

如需 JSON 語法和可用欄位的詳細資訊，請參閱[這裡][lnk-query-expression-guide]。您也可以查看[查詢運算式程式庫][lnk-query-samples]中的範例查詢。

### 依裝置和服務屬性進行查詢
具有 JSON 查詢運算式之後，即可查詢裝置對應項。呼叫 **queryDevices**，並檢查彙總查詢的 **result** 物件和所有其他查詢的 **devices** 物件。

```
registry.queryDevices(query, done)
```

### 依標記進行查詢
依標記進行查詢可讓您尋找裝置物件，而不需使用 JSON 查詢運算式。如果傳遞多個標記，則只會傳回具有所有標記的裝置物件。第二個參數是 **maxCount** (要傳回的裝置數目上限)。**maxCount** 的最大值是 1000。

```
registry.queryDevicesByTags(['bacon'], 100, done)
```

### 裝置實作
Azure IoT 中樞裝置管理用戶端程式庫會啟用查詢。只要同步處理您的裝置屬性 (如[教學課程︰如何使用裝置對應項][lnk-twin-tutorial]中所述)，即可對其進行查詢。只有在實體裝置連接到 IoT 中樞並提供初始值之後，才會提供裝置屬性。

## 後續步驟
若要深入了解 Azure IoT 中樞裝置管理功能，您可以瀏覽下列教學課程︰

* [如何使用裝置作業更新裝置韌體][lnk-tutorial-jobs]
* [啟用 IoT 閘道的受管理裝置][lnk-dm-gateway]
* [Azure IoT 中樞裝置管理用戶端程式庫簡介][lnk-library-c]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [設計您的解決方案][lnk-design]
* [開發人員指南][lnk-devguide]
* [使用閘道 SDK 模擬裝置][lnk-gateway]
* [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->