<properties
 pageTitle="如何產生 IoT 中樞安全性權杖 | Microsoft Azure"
 description="說明「IoT 中樞」使用的各類安全性權杖 (例如 SAS 和 X.509)，以及如何產生它們。"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="06/07/2016"
 ms.author="elioda"/>

# 使用 IoT 中樞安全性權杖和 X.509 憑證

IoT 中樞使用安全性權杖來驗證裝置和服務，以避免透過線路傳送金鑰。此外，安全性權杖有時效性和範圍的限制。[Azure IoT 中樞 SDK][lnk-apis-sdks] 能在不需要任何特殊組態的情況下自動產生權杖。不過在某些案例中，使用者必須直接產生及使用安全性權杖。這些案例包括直接使用 AMQP、MQTT 或 HTTP 介面或實作權杖服務模式，如 [IoT 中心指引][lnk-guidance-security]所述。

「IoT 中樞」也可允許裝置使用 X.509 憑證向「IoT 中樞」進行驗證。「IoT 中樞」針對透過 AMQP、透過 WebSocket 的 AMQP 及 HTTP 通訊協定的裝置，支援 X.509 型驗證。

本文章說明：

* 安全性權杖的格式，以及如何產生。
* 使用安全性權杖來驗證裝置和後端服務的主要使用案例。
* 針對裝置驗證支援的 X.509 憑證。
* 繫結至特定裝置之 X.509 用戶端憑證的註冊程序。
* 使用 X.509 用戶端憑證進行驗證之裝置與「IoT 中樞」間的執行階段流程。


## 安全性權杖結構
透過安全性權杖，您可以將 IoT 中樞內特定功能的限時存取權限授與裝置和服務。為了確保只有取得授權的裝置和服務可以連線，安全性權杖必須經過共用存取原則金鑰簽署，或經過與裝置身分識別一同儲存在身分識別登錄中的對稱金鑰簽署。

經過共用存取原則金鑰簽署的權限，能授與所有與共用存取原則權限相關聯之功能的存取權限。請參閱 [IoT 中樞開發人員指南的安全性小節][lnk-devguide-security]。另一方面，以裝置身分識別對稱金鑰簽署的權杖只會授與相關裝置身分識別的 **DeviceConnect** 權限。

安全性權杖具有下列格式：

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值：

| 值 | 說明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 簽章字串，格式為：`{URL-encoded-resourceURI} + "\n" + expiry`。**重要事項**：金鑰是從 base64 解碼而來，並且會做為用來執行 HMAC-SHA256 計算的金鑰。 |
| {resourceURI} | 可使用此權杖存取之端點的 URI 前置詞 (依區段)，其開頭為 IoT 中樞的主機名稱 (無通訊協定)。例如，`myHub.azure-devices.net/devices/device1` |
| {expiry} | 從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今秒數的 UTF8 字串。 |
| {URL-encoded-resourceURI} | 小寫資源 URI 的小寫 URL 編碼 |
| {policyName} | 此權杖所參考的共用存取原則名稱。在權杖參考裝置登錄認證的情況下不存在。 |

**前置詞的注意事項**︰URI 前置詞是依區段 (而不是依字元) 計算。例如，`/a/b` 是 `/a/b/c` 的前置詞，而不是 `/a/bc` 的前置詞。

這是從輸入 `resourceUri, signingKey, policyName, expiresInMins` 計算權杖的 Node 函數。下一節將詳細說明如何初始化不同權杖使用案例的不同輸入。

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };
 
 為了進行比較，對等的 Python 程式碼是︰
 
    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode
    
    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())
     
        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [AZURE.NOTE] 由於權杖的時效性會在 IoT 中樞機器上驗證，因此請務必將產生權杖之機器上的時鐘飄移降低最低。

## 使用 SAS 權杖做為裝置

利用安全性權杖取得「IoT 中樞」之 **DeviceConnect** 權限的方法有兩種：使用裝置身分識別金鑰，或使用共用存取原則金鑰。

此外，請務必注意所有可從裝置存取的功能，在設計上會於前置詞為 `/devices/{deviceId}` 的端點公開。

> [AZURE.IMPORTANT] IoT 中樞驗證特定裝置的唯一方法是使用裝置身分識別對稱金鑰。在使用共用存取原則來存取裝置功能的案例中，方案必須將發出安全性權杖之元件視為信任的子元件。

面向裝置的端點是 (不論通訊協定為何)︰

| 端點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | 傳送裝置到雲端的訊息。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` | 接收雲端到裝置的訊息。 |

### 使用身分識別登錄中的對稱金鑰

使用裝置身分識別對稱金鑰來產生權杖時，會省略權杖的 policyName (`skn`) 元素。

例如，為存取所有裝置功能而建立的權杖應具有下列參數︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices/{device id}`、
* 簽署金鑰︰`{device id}` 身分識別的任何對稱金鑰、
* 無原則名稱、
* 任何到期時間。

使用前述 Node 函式的範例為︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

結果 (將所有功能的存取權限授與 device1) 為︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] 您可以使用 .NET 工具[裝置總管][lnk-device-explorer]來產生安全權杖。

### 使用共用存取原則

從共用存取原則建立權杖時，原則名稱欄位 `skn` 必須設定為所使用之原則的名稱。原則也必須授與 **DeviceConnect** 權限。

使用共用存取原則來存取裝置功能的兩個主要案例包括︰

* [雲端通訊協定閘道][lnk-azure-protocol-gateway]、
* 用來實作自訂驗證配置的[權杖服務][lnk-devguide-security]。

由於共用存取原則可能可以授與以任何裝置身分連線的存取權限，因此在建立安全性權杖時，請務必使用正確的資源 URI。這點對權杖服務來說特別重要，因為它們必須使用資源 URI 來設定權杖的範圍，以便納入特定裝置。這點與通訊協定閘道的關聯性比較薄弱，因為它們已經在為所有裝置調節流量。

舉例來說，權杖服務如果使用名為 **device** 的預先建立共用存取原則，將會使用下列參數來建立權杖︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices/{device id}`、
* 簽署金鑰︰`device` 原則的其中一個金鑰、
* 原則名稱：`device`、
* 任何到期時間。

使用前述 Node 函式的範例為︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果 (將所有功能的存取權限授與 device1) 為︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

通訊協定閘道可以針對所有裝置都使用相同權杖，只要將資源 URI 設定為 `myhub.azure-devices.net/devices` 即可。

## 使用來自服務元件的安全性權杖

服務元件只能使用授與適當權限的共用存取原則來產生安全性權杖，如 [IoT 中樞開發人員指南的安全性小節][lnk-devguide-security]所述。

以下是在端點上公開的服務功能︰

| 端點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices` | 建立、更新、擷取及刪除裝置身分識別。 |
| `{iot hub host name}/messages/events` | 接收裝置到雲端的訊息。 |
| `{iot hub host name}/servicebound/feedback` | 接收雲端到裝置之訊息的意見反應。 |
| `{iot hub host name}/devicebound` | 傳送雲端到裝置的訊息。 |

舉例來說，服務如果使用名為 **registryRead** 的預先建立共用存取原則，將會使用下列參數來建立權杖︰

* 資源 URI：`{IoT hub name}.azure-devices.net/devices`、
* 簽署金鑰︰`registryRead` 原則的其中一個金鑰、
* 原則名稱：`registryRead`、
* 任何到期時間。

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果 (授與所有裝置身分識別的讀取權限) 會是︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## 支援的 X.509 憑證

您可以使用任何 X.509 憑證向「IoT 中樞」驗證裝置。其中包括：

-   **現有的 X.509 憑證**。裝置可能已經有與其關聯的 X.509 憑證。裝置可以使用此憑證向「IoT 中樞」進行驗證。

-   **自我產生及自我簽署的 X-509 憑證**。裝置製造商或公司內部的部署人員可以產生這些憑證，並將對應的私密金鑰 (和憑證) 存放在裝置上。您可以使用 [OpenSSL] 和 [Windows SelfSignedCertificate] 公用程式之類的工具來達到此目的。

-   **CA 簽署的 X.509 憑證**。您也可以使用「憑證授權單位」(CA) 所產生和簽署的 X.509 憑證來識別裝置，以及向「IoT 中樞」驗證裝置。

裝置可以使用 X.509 憑證或安全性權杖來進行驗證，但不可同時使用兩者。

## 註冊裝置的 X.509 用戶端憑證

[適用於 C# 的 Azure IoT 服務 SDK][lnk-service-sdk] (版本 1.0.8+) 支援註冊使用 X.509 用戶端憑證來進行驗證的裝置。其他 API (例如匯入/匯出裝置) 也支援 X.509 用戶端憑證。

### C# 支援

**RegistryManager** 類別提供一個程式設計方式來註冊裝置。特別是，**AddDeviceAsync** 和 **UpdateDeviceAsync** 方法可讓使用者在「IoT 中樞」裝置身分識別登錄中註冊和更新裝置。這兩種方法都會採用 **Device** 執行個體做為輸入。**Device** 類別包含 **Authentication** 屬性，這可讓使用者指定主要和次要 X.509 憑證指紋。憑證指紋代表 X.509 憑證的 SHA-1 雜湊 (使用二進位 DER 編碼來儲存)。使用者可以選擇指定主要指紋或次要指紋，或是同時指定兩者。支援主要和次要指紋是為了處理憑證變換情況。

> [AZURE.NOTE] 「IoT 中樞」並不需要也不會儲存整個 X.509 用戶端憑證，所需的只有指紋。

以下是使用 X.509 用戶端憑證來註冊裝置的範例 C# 程式碼片段︰

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

## 在執行階段作業期間使用 X.509 用戶端憑證

[適用於 .NET 的 Azure IoT 裝置 SDK][lnk-client-sdk] (版本 1.0.11+) 支援使用 X.509 用戶端憑證。

### C# 支援

**DeviceAuthenticationWithX509Certificate** 類別支援使用 X.509 用戶端憑證來建立 **DeviceClient** 執行個體。

以下是範例程式碼片段：

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[OpenSSL]: https://www.openssl.org/
[Windows SelfSignedCertificate]: https://technet.microsoft.com/library/hh848633
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device

<!---HONumber=AcomDC_0817_2016-->