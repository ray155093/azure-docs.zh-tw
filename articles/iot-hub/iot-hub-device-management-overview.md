<properties
 pageTitle="裝置管理概觀 | Microsoft Azure"
 description="Azure IoT 中樞裝置管理概觀：裝置對應項、裝置查詢、裝置工作"
 services="iot-hub"
 documentationCenter=""
 authors="ellenfosborne"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Azure IoT 中樞裝置管理概觀 (預覽)

Azure IoT 中樞裝置管理會啟用標準型 IoT 裝置管理，以讓您遠端管理、設定和更新您的裝置。

Azure IoT 裝置管理有三個主要概念：

1.  **裝置對應項：**IoT 中樞內實體裝置的呈現。

2.  **裝置查詢**：可讓您找到裝置對應項，以及產生裝置對應項的彙總了解。例如，您找不到韌體版本為 1.0 的所有裝置對應項。

3.  **裝置作業**︰要在一或多個實體裝置上執行的動作 (例如韌體更新、重新啟動和恢復出廠預設值)。

## 裝置對應項

裝置對應項是 Azure IoT 中實體裝置的呈現。**Microsoft.Azure.Devices.Device** 物件用來代表裝置對應項。

![][img-twin]

裝置對應項的元件如下：

1.  **裝置欄位：**裝置欄位是用於 IoT 中樞訊息和裝置管理的預先定義屬性。這些可協助 IoT 中樞識別實體裝置，並與之連接。裝置欄位不會同步處理到裝置，並且只會儲存在裝置對應項中。裝置欄位包含裝置識別碼和驗證資訊。

2.  **裝置屬性：**裝置屬性是描述實體裝置之預先定義的屬性字典。實體裝置是每個裝置屬性的主要存放區，而且是每個對應值的授權存放區。這些屬性的最終一致呈現會儲存在雲端的裝置對應項中。一致性和有效性受限於同步處理設定 (如[教學課程︰如何使用裝置對應項][lnk-tutorial-twin]中所述)。一些裝置屬性範例包含韌體版本、電池電量和製造商名稱。

3.  **服務屬性︰**服務屬性是開發人員加入服務屬性字典中的 **&lt;索引鍵,值&gt;** 組。這些屬性延伸裝置對應項的資料模型，可讓您更適當地描述您的裝置。服務屬性不會同步處理到裝置，並且只會儲存在雲端的裝置對應項中。其中一個服務屬性範例是 **&lt;下一個服務日期, 11/12/2017&gt;**，可用來依下一個服務日期來尋找裝置。

4.  **標記：**標記是可為任意字串 (而非字典屬性) 的服務屬性子集。它們可以用來標註裝置對應項，或將裝置組織成群組。標記不會同步處理到裝置，並且只會儲存在裝置對應項中。例如，如果您的裝置對應項代表實體卡車，則可以為卡車中的每一種貨物加入一個標記：**蘋果**、**柳橙**和**香蕉**。

## 裝置查詢

在上節中，您了解了裝置對應項的不同元件。現在，我們將說明如何根據裝置屬性、服務屬性或標記，在 IoT 中樞裝置登錄中找到裝置對應項。查詢使用時機範例是尋找您需要更新的裝置。您可以查詢所有具有指定韌體版本的裝置，以及將結果饋送到特定動作 (在 IoT 中樞內稱為裝置作業，下一節將予以說明)。

您可以使用標記和屬性進行查詢︰

-   若要使用標記來查詢裝置對應項，您可以傳遞字串陣列，而且查詢會傳回加上所有這些字串的裝置集合。

-   若要使用服務屬性或裝置屬性來查詢裝置對應項，您可以使用 JSON 查詢運算式。下列範例示範如何使用具有索引鍵 **FirmwareVersion** 和值 **1.0** 的裝置屬性來查詢所有裝置。您可以看到屬性的「類型」為「裝置」，這表示我們是根據裝置屬性進行查詢，而非服務屬性︰

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## 裝置作業

裝置管理的下一個概念是裝置作業，可協調多個裝置上的多步驟協調流程。

Azure IoT 中樞裝置管理目前提供六種類型的裝置作業 (我們會在客戶需要其他作業時新增作業)：

- **韌體更新**：更新實體裝置上的韌體 (或 OS 映像)。
- **重新啟動**：重新啟動實體裝置。
- **恢復出廠預設值**：將實體裝置的韌體 (或 OS 映像) 還原為儲存在裝置上的原廠提供備份映像。
- **組態更新**：設定實體裝置上執行的 IoT 中樞用戶端代理程式。
- **讀取裝置屬性**︰取得實體裝置上裝置屬性的最新值。
- **寫入裝置屬性**︰變更實體裝置上的裝置屬性。

如需如何使用所有這些作業的詳細資訊，請參閱 [C# 和 node.js 的 API 文件][lnk-apidocs]。

作業可以在多個裝置上作業。當您啟動作業時，會為所有這些裝置各建立相關聯的子工作。子工作是在單一裝置上作業。每項子工作都會有其父工作的指標。父工作只是子工作的容器，並不會實作任何邏輯來區別裝置類型 (例如更新 Intel Edison 與更新 Raspberry Pi)。下圖說明父工作、其子系與相關聯實體裝置之間的關聯性。

![][img-jobs]

您可以查詢作業記錄，了解所啟動作業的狀態。如需一些範例查詢，請參閱[我們的查詢程式庫][lnk-query-samples]。

## 裝置實作

現在，我們已經涵蓋服務端概念，接下來將討論如何建立具有管理功能的裝置。裝置上的 Azure IoT 中樞裝置管理用戶端程式庫提供必要的構件來實作您實體裝置與 IoT 中樞之間的通訊。

Azure IoT 中樞 DM 用戶端程式庫會抽取 [LWM2M][lnk-lwm2m] 標準和 CoAP 型要求/回應通訊協定。因此，程式庫的裝置機型為「物件」和「資源定義」：

-   物件會描述系統中的一組一致功能實體 (例如裝置和韌體更新)。
-   資源會描述這些物件中包含的屬性或動作 (例如電池電量資訊和重新啟動動作)。

當您使用 Azure IoT 中樞 DM 用戶端程式庫時，需要實作實體裝置上每個資源的讀取、寫入和執行作業回呼。此程式庫會處理屬性變更時的 IoT 中樞非同步更新。

下圖顯示 IoT 中樞用戶端代理程式中所需的不同元件。

![][img-client]

您可以在[適用於 C 的 Azure IoT 中樞裝置管理用戶端程式庫簡介][lnk-library-c]中深入了解實體裝置上的實作。

## 後續步驟

若要深入了解 Azure IoT 中樞裝置管理功能，您可以瀏覽下列教學課程︰

- [開始使用 Azure IoT 中樞裝置管理][lnk-get-started]

- [如何使用裝置對應項][lnk-tutorial-twin]

- [如何使用查詢找出裝置對應項][lnk-tutorial-queries]

- [如何使用裝置作業更新裝置韌體][lnk-tutorial-jobs]

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0504_2016-->