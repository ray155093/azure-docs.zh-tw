<properties
 pageTitle="IoT 中樞解決方案指引 | Microsoft Azure"
 description="使用 Azure IoT 中樞來開發 IoT 解決方案的閘道器、裝置佈建及驗證的相關指引主題。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/19/2016"
 ms.author="dobett"/>

# 設計您的解決方案

本文提供如何在物聯網 (IoT) 解決方案中設計下列功能的指引：

- 裝置佈建
- 現場閘道器
- 裝置驗證
- 裝置活動訊號

## 裝置佈建

IoT 解決方案會儲存個別裝置的相關資料，例如：

- 裝置身分識別和驗證金鑰
- 裝置硬體類型和版本
- 裝置狀態
- 軟體版本和功能
- 裝置命令歷程記錄

給定的 IoT 解決方案儲存的裝置資料取決於該解決方案的特定需求。但是解決方案至少必須儲存裝置身分識別和驗證金鑰。Azure IoT 中樞包含[身分識別登錄][lnk-devguide-identityregistry]，其可以儲存每個裝置的值，例如識別碼、驗證金鑰和狀態碼。解決方案可以使用其他 Azure 服務 (例如資料表、blob 或 Azure DocumentDB) 來儲存任何其他裝置資料。

*裝置佈建*是將初始裝置資料加入至解決方案中存放區的程序。若要讓新裝置可連接到您的中樞，必須將新裝置識別碼和金鑰加入至 [IoT 中樞身分識別登錄][lnk-devguide-identityregistry]。做為佈建程序的一部分，您可能需要初始化其他解決方案存放區中的裝置特定資料。

[IoT 中樞身分識別登錄 API][lnk-devguide-identityregistry] 可讓您將 IoT 中樞整合到您的佈建程序。

## 現場閘道器

在 IoT 解決方案中，*現場閘道器*位於您的裝置和 IoT 中樞之間。它通常位於接近您的裝置的位置。您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊。現場閘道器會使用 IoT 中樞所支援的通訊協定來與 IoT 中樞通訊。現場閘道可以是高度特殊化硬體或低功率電腦，其執行完成閘道所想要之端對端案例的軟體。

現場閘道器與簡單的流量路由裝置 (例如網路位址轉譯 (NAT) 裝置或防火牆) 不同，因為它通常會在解決方案內管理存取和資訊流程中扮演主動的角色。例如，現場閘道器可以：

- **加入新舊裝置的支援**：有數百萬個新舊感應器和傳動器無法直接將資料傳送至雲端。這些裝置所使用的通訊協定不適用於網際網路、未實作加密，或無法儲存身分識別憑證。使用閘道可減少這些裝置的連接負擔和成本。
- **執行邊緣分析**：有許多作業可以在本機完成，以減少與雲端交換的資料量。範例包含資料篩選、批次處理和壓縮。它也可能需要執行某些計算，例如，資料清理，或使用內部部署上的即時資料來評分機器學習模型。
- **減少延遲**：當您嘗試防止製造行關閉或還原電子服務時，毫秒十分重要。分析接近已收集資料之裝置的資料，可能會造成避開災害與串聯系統失敗之間的差異。
- **節省網路頻寬**︰一般近海石油平台每天都會產生 1 TB 到 2 TB 的資料。波音 787 每次飛行都會建立一半 TB 的資料。將大量資料從數千或數十萬個邊緣裝置傳輸至雲端並不實際。也不需要，因為許多重要分析不需要進行雲端規模的處理和儲存。
- **可靠地作業**：IoT 資料越來越常用於影響市民安全和重要基礎結構的決策。間歇性雲端連接不會危及基礎結構和資料的完整性和可用性。使用儲存和轉寄這類功能以在本機收集和處理資料，然後在適當時傳送至雲端，協助您建立可靠的方案。
- **解決隱私權和安全性顧慮**：需要保護 IoT 裝置和其產生的資料。閘道可以提供服務，例如，隔離裝置與開放式網際網路、為本身無法提供加密和身分識別服務的裝置提供加密和身分識別服務、保護在本機緩衝處理或儲存的資料，以及在透過網際網路傳送個人識別資訊之前將其移除。

### 其他考量

您可以使用 [Azure IoT 閘道 SDK][lnk-gateway-sdk] 來實作現場閘道。此 SDK 提供特定功能，例如可以對從多個裝置到相同 IoT 中樞連接的通訊進行多工處理。

## 自訂裝置驗證

您可以使用 IoT 中樞[裝置身分識別登錄][lnk-devguide-identityregistry]，利用[權杖][lnk-sas-token]來設定每一裝置的安全性認證和存取控制。如果 IoT 解決方案已經大幅投資自訂裝置身分識別登錄及/或驗證配置，您可以藉由建立*權杖服務*，將這個現有基礎結構與 IoT 中樞整合。如此一來，您可以在解決方案中使用其他 IoT 功能。

權杖服務是自訂雲端服務。建立具備 **DeviceConnect** 權限的 IoT 中樞共用存取原則，以建立「裝置範圍」權杖。這些權杖可讓裝置連接到 IoT 中樞。

  ![權杖服務模式的步驟][img-tokenservice]

以下是權杖服務模式的主要步驟：

1. 為您的 IoT 中樞建立具備 **DeviceConnect** 權限的 [IoT 中樞共用存取原則][lnk-devguide-security]。您可以在 [Azure 入口網站][lnk-portal]中或以程式設計方式建立此原則。權杖服務會使用此原則簽署它所建立的權杖。
2. 當裝置需要存取 IoT 中樞時，它會向您的權杖服務要求已簽署的權杖。裝置可以使用您的自訂裝置身分識別登錄/驗證配置來進行驗證，以判斷權杖服務用來建立權杖的裝置身分識別。
3. 權杖服務會傳回權杖。使用 `/devices/{deviceId}` 做為 `resourceURI` (其中 `deviceId` 是要驗證的裝置)，並根據 [IoT 中樞開發人員指南的安全性一節][lnk-devguide-security]建立權杖。權杖服務會使用共用存取原則來建構權杖。
4. 裝置直接透過 IoT 中樞使用權杖。

> [AZURE.NOTE] 您可以使用 .NET 類別 [SharedAccessSignatureBuilder][lnk-dotnet-sas] 或 Java 類別 [IotHubServiceSasToken][lnk-java-sas] 在權杖服務中建立權杖。

權杖服務可以視需要設定權杖到期日。權杖到期時，IoT 中樞會切斷裝置連線。然後，裝置必須向權杖服務要求新權杖。如果您使用過短的到期時間，這會增加裝置與權杖服務上的負載。

為了讓裝置連線至中樞，即使裝置使用權杖而不是裝置金鑰來連線，您仍必須將它加入 IoT 中樞裝置身分識別登錄。因此，您可以在裝置使用權杖驗證時，利用在 [IoT 中樞識別登錄][lnk-devguide-identityregistry]中啟用或停用裝置身分識別，繼續使用每一裝置存取控制。如此可減輕使用較長到期時間權杖的風險。

### 和自訂閘道器的比較

權杖服務模式為使用 IoT 中樞實作自訂身分識別登錄/驗證配置的建議方式。這麼建議是因為 IoT 中樞會繼續處理大部份的解決方案流量。不過，在一些情況下，自訂驗證配置和通訊協定過度交織，因此需要可處理所有流量 (*自訂閘道器*) 的服務。[傳輸層安全性 (TLS) 和預先共用金鑰 (PSK)][lnk-tls-psk] 是服務範例之一。如需詳細資訊，請參閱[通訊協定閘道器][lnk-protocols]主題。

## 裝置活動訊號 <a id="heartbeat"></a>

[IoT 中樞身分識別登錄][lnk-devguide-identityregistry]包含稱為 **connectionState** 的欄位。您只應該在開發和偵錯期間使用 **connectionState** 欄位，IoT 方案不應該在執行階段查詢該欄位 (例如，為了檢查裝置是否已連接，以便決定是否要傳送雲端到裝置的訊息或 SMS)。如果 IoT 方案需要知道裝置是否已連接 (在執行階段，或比 **connectionState** 屬性所提供的更精確時)，方案應該實作「活動訊號模式」。

在活動訊號模式中，裝置每隔固定時間就會至少傳送一次裝置到雲端的訊息 (例如，每小時至少一次)。這表示即使裝置沒有任何要傳送的資料，它仍會傳送空的裝置到雲端的訊息 (通常具有可供識別其屬於活動訊號的屬性)。在服務端，解決方案會維護一份地圖，裡面有每個裝置所收到的最後一次活動訊號，並假設如果裝置沒有在預期時間內收到活動訊號訊息，即表示裝置有問題。

更複雜的實作可以包含來自[作業監視][lnk-devguide-opmon]的資訊，以便識別嘗試連接或通訊但失敗的裝置。當您實作活動訊號模式時，請務必檢查 [IoT 中樞配額與節流][]。

> [AZURE.NOTE] 如果 IoT 解決方案只需要裝置連線狀態來判斷是否要傳送雲端到裝置訊息，而且訊息不會廣播到大量的裝置組合，則要考慮使用的較簡單模式就是使用較短的到期時間。這可達到與使用活動訊號模式來維護裝置連線狀態登錄相同的效果，同時更具效率。它也能夠藉由要求訊息收條，收到 IoT 中樞的通知，告知哪些裝置能夠收到訊息，而哪些裝置不在線上或故障。如需 C2D 訊息的詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-messaging]。

## 後續步驟

若要深入了解如何規劃 IoT 中樞部署，請參閱：

- [MQTT 支援][lnk-mqtt]
- [支援的裝置][lnk-devices]
- [支援其他通訊協定][lnk-protocols]
- [與事件中樞比較][lnk-compare]
- [縮放、HA 及 DR][lnk-scaling]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [使用範例 UI 探索裝置管理][lnk-dmui]
- [使用閘道 SDK 模擬裝置][lnk-gateway]
- [使用 Azure 入口網站管理 IoT 中樞][lnk-portal-manage]
- [徹底保護您的 IoT 解決方案][lnk-securing]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279

[lnk-portal]: https://portal.azure.com
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[IoT 中樞配額與節流]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md
[lnk-sas-token]: iot-hub-sas-tokens.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0831_2016-->