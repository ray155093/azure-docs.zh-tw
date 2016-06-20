<properties
 pageTitle="裝置管理程式庫簡介 | Microsoft Azure"
 description="Azure IoT 中樞裝置管理 (DM) 用戶端程式庫"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Azure IoT 中樞裝置管理 (DM) 用戶端程式庫簡介

## 概觀

Azure IoT 中樞裝置管理用戶端程式庫可讓您使用 Azure IoT 中樞管理 IoT 裝置。「管理」包含重新啟動、恢復出廠預設值和更新韌體等動作。目前我們提供的是各平台通用的 C 程式庫，但我們很快就會增加對其他語言的支援。如 [Azure IoT 中樞裝置管理概觀][lnk-dm-overview]所述，IoT 中樞的裝置管理蘊含三個主要概念︰

- 裝置對應項
- 裝置作業
- 裝置查詢

如果您不熟悉上述概念，建議您先閱讀概觀再繼續本文。這些概念全都與用戶端程式庫緊密相關。

DM 用戶端程式庫在裝置管理中擔負兩項重責大任︰

- 將實體裝置的屬性與其在 IoT 中樞內的裝置對應項進行同步處理
- 設計由 IoT 中樞傳送到裝置的裝置作業

實體裝置的裝置屬性 (例如電池電量和序號) 會定期與雲端式裝置對應項同步處理，以讓 IoT 中樞隨時有每個實體裝置的最新檢視 (只要裝置有連線)。

服務在與實體裝置互動時，主要是透過裝置作業和裝置對應項。IoT 中樞提供了下列作業類型。DM 用戶端程式庫可處理大部分的裝置作業協調工作，但身為開發人員的您必須負責在您的裝置上實作必要的回呼，以支援每個作業類型。

1.	**韌體更新**：更新實體裝置上的韌體 (或作業系統映像)
2.	**重新啟動**：重新啟動實體裝置
3.	**恢復出廠預設值**︰將實體裝置的韌體 (或 OS 映像) 恢復為儲存在裝置上的原廠提供備份映像
4.	**組態更新**：設定實體裝置上執行的 IoT 中樞用戶端代理程式
5.	**讀取裝置屬性**︰取得實體裝置上裝置屬性的最新值
6.	**寫入裝置屬性**︰變更實體裝置上的裝置屬性

下列各節將逐步引導您設計用戶端程式庫架構，以及提供您有關如何在裝置上實作不同裝置物件的指導方針。

## 裝置管理用戶端程式庫的設計原則與功能性概念
DM 用戶端程式庫在設計時已考慮到可攜性和跨平台整合。這一點是透過下列設計決策所實現︰

1.	以 LWM2M over COAP 標準通訊協定為基礎，以適應各種不同裝置的擴充性。
2.	以 ANSI C99 撰寫，以促成各種平台的可攜性。
3.	透過 TCP/TLS 和 Azure IoT 中樞驗證 (SAS 權杖) 提供保護，因此可用在高安全性案例中。
4.	以 [Eclipse Wakaama][lnk-Wakaama] OSS 專案為基礎，以利用現有程式碼並回饋社群。

### 相關的 LWM2M 概念
我們選擇 LWM2M 標準以適應各種不同裝置的擴充性。為了簡化開發經驗，我們擷取了大部分的通訊協定。不過，請務必了解程式庫的基本原則，尤其是其資料模型和資料傳輸方式。

#### 物件和資源︰LWM2M 中的資料模型
LWM2M 資料模型引進了物件和資源的概念︰

- **物件**會描述系統中的一組一致功能的實體，例如裝置與韌體更新。
- **資源**會描述這些物件中包含的屬性或動作，例如電池電量資訊和重新啟動動作。

請注意，此模型中有兩個「一對多」關係︰

- **裝置和物件**︰每個裝置都可以有多個物件。例如，Contoso 裝置必須具有「裝置物件」和「伺服器物件」(我們會在下一節詳細說明物件)。
- **物件和資源**︰每個物件都可以有多個資源。舉例來說，物件可以包含多個 Contoso 裝置韌體更新資源，例如新映像儲存所在的封裝 URI。

#### 觀察/通知模式︰資料在 LWM2M 中的傳輸方式
除了上述概念，也請務必了解資料從裝置流動至服務的方式。為了做到這一點，LWM2M 會定義「觀察/通知」模式。實體裝置連接到服務時，IoT 中樞會初始化所選取裝置屬性上的「觀察」。然後，實體裝置會「通知」服務有關裝置屬性的變更。

在我們的用戶端程式庫中，我們已實作觀察/通知模式來做為將裝置管理資料從裝置傳送至 IoT 中樞的方式。此模式由兩個參數所控制︰

- **最小期限**：裝置可針對所觀察的屬性延遲傳送更新的一段時間。此期限設定為 5 分鐘。因此，裝置最多每隔 5 分鐘就會針對所觀察的屬性傳送更新，即使值是以更高的頻率在變更也是如此。這表示，如果屬性每分鐘都在變更，服務只會看到第 5 分鐘時的最後一次變更。

- **最大期限**︰不管所觀察的屬性是否變更，裝置針對屬性值傳送更新的一段時間。此期限設定為 6 小時。因此，裝置最少要每 6 小時才會針對所觀察的屬性值傳送更新，即使值尚未變更也是如此。

> [AZURE.NOTE]  這些參數的唯一例外是，用於韌體更新作業的屬性會將最小期限設為 30 秒。這是因為在作業執行期間，這些屬性會非常頻繁地變更，因此我們將最小期限減少以加快更新程序。

## 用戶端程式庫的功能和架構
如我們在概觀中看到的，用戶端程式庫會負責處理兩項主要功能︰

- 同步處理實體裝置與其在 IoT 中樞內的裝置對應項。
- 設計由 IoT 中樞傳送到裝置的裝置作業。

本節將深入探討這兩項功能。

### 同步處理實體裝置與其裝置對應項
用戶端程式庫使用觀察/通知模式讓裝置對應項保持最新狀態。請記住，裝置對應項是實體裝置在服務端的代表。為了執行這項同步處理，將會進行下列程序︰

1. 裝置向服務進行註冊，通常會在初始化期間。範例：「我是裝置，我有裝置識別碼 Contoso 和存取權杖 Y。」
2. 服務會觀察物件上的資源。範例：「隨時讓我知道我的 Contoso 裝置的電池電量。」
3. 裝置將資源值通知服務。通知頻率是根據最小期限和最大期限。範例：「下午 5:00 電池電量 99%，下午 5:05 電池電量 90%，依此類推」。

### 設計裝置作業︰裝置對應項和裝置作業如何一起運作
為了處理實體裝置，服務必須尋找相關聯的裝置對應項。透過查詢屬性或搜尋特定識別碼即可做到這點。在得知對應項識別碼 (並因此比對到實體裝置) 後，服務就可以在實體裝置上開始裝置作業。

裝置作業代表一組不同的命令，這些命令則代表我們想要執行的特定程序 (例如︰韌體更新步驟)。裝置作業可以包含多個步驟︰

1.	裝置對應項具有代表裝置作業狀態的屬性。
2.	為了在裝置作業的不同程序間前進，裝置對應項的屬性必須是特定值，因此實體裝置必須設定正確的屬性，以便能夠同步至裝置對應項並讓作業前進到下一步。

如果程序中包含多個步驟，此順序會不斷重複 (例如︰在韌體更新期間，裝置對應項會在不同步驟變更其屬性多次，然後才會將作業視為已完成)。

## 在用戶端中實作裝置管理的指導方針
在先前幾節當中，我們已了解裝置管理用戶端程式庫的功能、其設計原則，以及它與 LWM2M 有何關聯。現在，我們要將重心放在說明這些環節在執行階段中要如何拼湊在一起。

基本上，用戶端程式庫會處理裝置與服務之間的通訊，因此剩下的工作只有實作裝置特定邏輯。這包含兩個部分：

1.	**實作裝置特定詳細資料**︰這包含撰寫裝置特定邏輯以在適當回呼中執行服務所要求的動作 (例如：下載韌體封裝)。用於韌體更新封裝的此回呼範例列在下面。您可以在[這裡][lnk-github1]的資料夾內所含的 .c 檔案中找到回呼。

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**在屬性變更時通知用戶端程式庫**︰您可以透過呼叫[這裡][lnk-github2]的資料夾內所含的 .h 檔案中的對應 set 函式來達成此目的。

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### 您必須在 DM 用戶端程式庫中實作的物件

我們剛才說明了如何實作裝置特定邏輯以執行裝置作業。現在，我們要說明哪些物件可供您使用。

其中有些物件是必要的，這表示您必須實作裝置特定邏輯，才能讓它成為「IoT 中樞」裝置管理的一部分。有些則是選擇性的，因此建議您可以根據服務的需求做選擇 (例如︰您可以選擇不要使用 IoT 中樞來進行韌體更新)。以下是各個物件的說明︰

- **裝置物件 (必要)**︰提供裝置特定資訊，例如製造商資訊、型號、序號、裝置時間。服務可以讀取這項資訊，並在某些情況下加以更新。它也會定義服務可以對裝置執行的兩個動作︰重新啟動和恢復出廠預設值。
- **伺服器物件 (必要)**︰包含用來連接到 IoT 中樞的連接參數和設定，例如註冊的存留期和傳輸繫結。服務只能讀取這項資訊。
- **設定物件 (選擇性)**︰包含可從裝置進行查詢或從服務推送至裝置以幫助設定裝置的使用者定義的組態資訊。服務可以讀取和更新這項資訊。
- **韌體更新物件 (選擇性)**︰提供服務可以叫用的韌體更新動作。它也會提供韌體封裝位置和進行中的韌體更新作業狀態等資訊。

這些物件之中的每一個都有一組相關聯的資源 (請回想「一對多」關聯)。本文結尾處有 Azure IoT 中樞裝置管理中所支援之 LWM2M 物件和其所有相關聯資源的清單。

> [AZURE.NOTE] 目前的系統版本不支援自訂裝置屬性、多個資源執行個體和多個物件執行個體。

### 總整理

您可以在下面找到一張將所有不同部分組合在一起後的圖表。在右邊的藍色部分，您會看到裝置管理用戶端程式庫的不同元件︰物件、處理常式和通知方法。左邊的綠色部分則代表您必須在裝置應用程式層級撰寫的邏輯。用戶端程式庫會連接應用程式邏輯與 IoT 中樞，確保能正確地處理通訊和設計。

下圖顯示 DM 用戶端程式庫的元件。

![][img-library-overview]

## 後續步驟︰獲得實際操作經驗
本文說明了對 C 使用 IoT 中樞裝置管理用戶端程式庫的基本概念。

若要獲得實際操作經驗，您可以存取下列資源︰

- Intel Edison 韌體更新範例︰使用 Intel Edison 裝置並已啟用裝置管理功能的範例。請參閱 [iotdm\_edison\_sample][lnk-edison-sample]。
- 模擬的裝置範例︰在 Linux 和 Windows 裝置上執行的各平台通用之裝置的範例。請參閱 [iotdm\_simple\_sample][lnk-simple-sample]
- 若要深入了解 LWM2M 物件，請參閱 [OMA LWM2M 物件和資源登錄][lnk-oma]

## 附錄︰目前支援的 LWM2M 物件和資源

### 裝置物件

| 資源名稱 | 資源上允許的遠端作業 | 類型 | 範圍與單位 | 說明 |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| 製造商 | 讀取 | String | | 製造商名稱 |
| ModelNumber | 讀取 | String | | 機型識別碼 (製造商指定的字串) |
| DeviceType | 讀取 | String | | 裝置類型 (製造商指定的字串)<br/>注意︰這會對應至伺服器端 API **SystemPropertyNames.DeviceDescription** |
| SerialNumber | 讀取 | String | | 裝置的序號 |
| FirmwareVersion | 讀取 | String | | 裝置目前的韌體版本 |
| HardwareVersion | 讀取 | String | | 裝置目前的硬體版本。 |
| 重新啟動 | 執行 | | | 重新啟動裝置。 |
| FactoryReset | 執行 | | | 對裝置執行恢復出廠預設值，讓裝置具有和初始部署時相同的組態。 |
| CurrentTime | 讀取<br/>寫入 | 時間 | | 裝置目前的 UNIX 時間。用戶端應該要負責在每過一秒時增加此時間值。<br/>DM 伺服器能夠寫入至此資源，以讓用戶端與伺服器上的時間保持同步。 |
| UTCOffset | 讀取<br/>寫入 | String | | UTC 時差會生效。 |
| 時區 | 讀取<br/>寫入 | String | | 指出裝置所在的時區。 |
| MemoryFree | 讀取 | Integer | KB | 裝置中目前估計可供儲存資料和軟體的可用儲存空間記憶體 |
| MemoryTotal | 讀取 | Integer | KB | 裝置中可以儲存資料和軟體的儲存空間總容量 |
| BatteryLevel | 讀取 | Integer | 0-100% | 目前的電池電量，以百分比表示 (從 0 到 100) |
| BatteryStatus | 讀取 | Integer | 0-6 | **0**：電池運作正常且未開啟電源。<br/>**1**：電池正在充電。<br/>**2**：電池已充飽電且連接電網。<br/>**3**：電池損壞。<br/>**4**：電池電量不足。<br/>**5**：沒有電池。<br/> **6**︰無法取得電池資訊。 |

### 韌體更新物件

| 資源名稱 | 作業 | 類型 | 範圍與單位 | 說明 |
|----------------|-----------|---------|-----------------|-------------|
| Package | 寫入 | Opaque | | 二進位格式的韌體封裝。<br/>對應至服務 API：<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageURI | 寫入 | String | 0-255 個位元組 | 可供裝置下載韌體封裝的 URI。<br/>對應至服務 API：**SystemPropertyNames.FirmwarePackageUri** |
| 更新 | 執行 | | | 使用儲存在封裝中的韌體封裝或使用從封裝 URI 下載的韌體來更新韌體。<br/>對應至服務 API：<br/>**ScheduleFirmwareUpdateAsync** |
| 狀況 | 讀取 | Integer | 1-3 | 韌體更新程序的狀態︰<br/>**1**：閒置。這可能在下載韌體封裝之前或套用韌體封裝之後發生。<br/>**2**：正在下載韌體封裝。<br/>**3**：韌體封裝已下載。<br/> 對應至服務 API：**SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | 讀取 | Integer | 0-6 | 韌體的下載或更新結果<br/>**0**：預設值。<br/>**1**：韌體更新成功。<br/>**2**：儲存空間不足，無法儲存新的韌體封裝。<br/>**3**：韌體封裝下載期間耗盡記憶體。<br/>**4**：韌體封裝下載期間連線中斷。<br/>**5**：新下載的封裝未通過 CRC 檢查。<br/>**6**：不支援的韌體封裝類型。<br/>**7**：無效的 URI。對應至服務 API：**SystemPropertyNames.FirmwareUpdateResult** |
| PkgName | 讀取 | String | 0-255 個位元組 | **封裝**資源所參考之韌體封裝的描述性名稱<br/>對應至服務 API：<br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | 讀取 | String | 0-255 個位元組 | **封裝**資源所參考之韌體封裝的版本<br/>對應至服務 API：<br/>**SystemPropertyNames.FirmwarePackageVersion** |

### LWM2M 伺服器物件

| 資源名稱 | 作業 | 類型 | 範圍與單位 | 說明 |
|------------------------|------------|---------|-----------------|---------------|
| 預設最小期限 | 讀取寫入 | Integer | 秒 | 裝置可針對所觀察的屬性延遲傳送更新的一段時間。例如，若給定 5 分鐘的 **DefaultMinPeriod**，裝置最多每隔 5 分鐘就會針對所觀察的屬性傳送更新，即使值是以更高的頻率在變更也是如此。對應至服務 API：**SystemPropertyNames.DefaultMinPeriod** |
| 預設最大期限 | 讀取寫入 | Integer | 秒 | 不管所觀察的屬性是否變更，裝置針對屬性值傳送更新的一段時間 (以秒為單位)。例如，若給定 6 小時的 **DefaultMaxPeriod**，所觀察的屬性最少要每 6 小時才會針對該屬性的值傳送更新，不管資源是否有變更都是如此。<br/>對應至服務 API：<br/>**SystemPropertyNames.DefaultMaxPeriod** |
| 存留期 | 讀取寫入 | Integer | 秒 | 裝置的註冊存留期。必須在此存留期內從裝置接收新的註冊或更新要求，否則裝置會對服務取消註冊。<br/>對應至服務 API：<br/>**SystemPropertyNames.RegistrationLifetime** |

### 設定物件

| 資源名稱 | 作業 | 類型 | 範圍與單位 | 說明 |
|---------------|------------|--------|-----------------|-------------|
| 名稱 | 讀取寫入 | String | | 可唯一識別要讀取或更新之裝置組態的名稱。 |
| 值 | 讀取寫入 | String | | 可唯一識別要讀取或更新的組態值。 |
| 套用 | 執行 | | | 對裝置套用組態變更。 |

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-run-linux]: http://TODO
[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

<!---HONumber=AcomDC_0608_2016-->