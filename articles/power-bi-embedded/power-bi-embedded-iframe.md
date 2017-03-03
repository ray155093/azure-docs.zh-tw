---
title: "如何使用 Power BI Embedded 搭配 REST | Microsoft Docs"
description: "了解如何使用 Power BI Embedded 搭配 REST  "
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 8bcef780-cca0-4f30-9a9b-9daa1a7ae865
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 02/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 89e16687f858708cdfd1432114c39bd9109dc6ac
ms.openlocfilehash: 31624b9d15772a4f08cf013ac713b3aa636acfca
ms.lasthandoff: 02/07/2017


---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>如何使用 Power BI Embedded 搭配 REST

## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI Embedded：了解功能與用途

在官方的 [Power BI Embedded 網站](https://azure.microsoft.com/services/power-bi-embedded/)中已說明 Power BI Embedded 的概觀，但在深入了解使用它來搭配 REST 的詳細資料之前，讓我們先快速了解一下。

這其實很簡單。 您可能想在自己的應用程式中使用 [Power BI](https://powerbi.microsoft.com) 的動態資料視覺效果。

大部分的自訂應用程式需要為它們的客戶傳遞資料，那些客戶不一定是它們本身組織內的使用者。 例如，如果您要同時為公司 A 和 公司 B 提供某些服務，公司 A 中的使用者應該只會看到他們公司 A 本身的資料。也就是需要透過多重租用來傳遞。

自訂應用程式也可以提供自己的驗證方法，例如表單驗證、基本驗證等等。 接著，內嵌解決方案必須與現有的驗證方法安全地共同作業。 同時，使用者也必須可以使用那些 ISV 應用程式，而不需要另外購買 Power BI 訂用帳戶或授權。

 **Power BI Embedded** 正是針對這類案例所設計。 我們現在已經完成快速介紹，接著就讓我們深入了解一些詳細資料

您可以使用 .NET \(C#) 或 Node.js SDK 來輕鬆建立含有 Power BI Embedded 的應用程式。 但是，在本文中，我們將不使用 SDK 來說明關於 Power BI 的 HTTP 流程 \(incl. AuthN)。 了解這個流程，您就可以 **使用任何程式設計語言**建置應用程式，並深入了解 Power BI Embedded 的本質。

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>建立 Power BI 工作區集合，並取得存取金鑰 \(佈建)

Power BI Embedded 是其中一項 Azure 服務。 只有使用 Azure 入口網站的 ISV 要支付使用費 \(依據每小時的使用者工作階段計費)，檢視報表的使用者則不需要收費，甚至不需要 Azure 訂用帳戶。
在開始開發我們的應用程式之前，我們必須使用 Azure 入口網站建立 **Power BI 工作區集合** 。

每個 Power BI Embedded 的工作區是各客戶 (租用戶) 的工作區，我們可以在每個工作區集合中新增多個工作區。 每個工作區集合會使用相同的存取金鑰。 實際上，工作區集合是 Power BI Embedded 的安全性界限。

![](media/power-bi-embedded-iframe/create-workspace.png)

當我們完成建立工作區集合後，請從 Azure 入口網站複製存取金鑰。

![](media/power-bi-embedded-iframe/copy-access-key.png)

> [!NOTE]
> 我們也可以佈建工作區集合，然後透過 REST API 取得存取金鑰。 若要深入了解，請參閱 [Power BI Resource Provider APIs (Power BI 資源提供者 API)](https://msdn.microsoft.com/library/azure/mt712306.aspx)。

## <a name="create-pbix-file-with-power-bi-desktop"></a>使用 Power BI Desktop 建立 .pbix 檔案

接下來，我們必須建立資料連接與要內嵌的報表。
此工作中沒有任何程式設計或程式碼。 我們只使用 Power BI Desktop。
在本文中，我們不會探討如何使用 Power BI Desktop。 如果您在此處需要一些說明，請參閱 [開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)。 在我們的範例中，我們只使用 [零售分析範例](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/)。

![](media/power-bi-embedded-iframe/power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>建立 Power BI 工作區

現在已經完成所有的佈建，我們可以透過 REST API 開始在工作區集合中建立客戶的工作區。 下列 HTTP POST 要求 (REST) 會在我們現有的工作區集合中建立新的工作區。 這是 [POST 工作區 API](https://msdn.microsoft.com/library/azure/mt711503.aspx)。 在本範例中，工作區集合名稱是 **mypbiapp**。 我們只要將先前複製的存取金鑰設定為 **AppKey**。 這是非常簡單的驗證！

**HTTP 要求**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

傳回的 **workspaceId** 會用於後續的 API 呼叫。 我們的應用程式必須保留這個值。

## <a name="import-pbix-file-into-the-workspace"></a>將 .pbix 檔案匯入工作區

工作區中的每個報表都會對應到單一 Power BI Desktop 檔案，其中含有資料集 \(包括資料來源設定)。 我們可以將我們的 .pbix 檔案匯入工作區，如下列程式碼所示。 如您所見，我們可以使用 http 中的 MIME multipart 上傳 .pbix 檔案的二進位檔。

Uri 片段 **32960a09-6366-4208-a8bb-9e0678cdbb9d** 是工作區識別碼，而查詢參數 **datasetDisplayName** 是要建立的資料集名稱。 建立的資料集會保存 .pbix 檔案中所有和資料相關的成品，例如匯入的資料、資料來源的指標等等。

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

此匯入工作可能會執行一段時間。 完成時，我們的應用程式可以使用匯入識別碼要求工作狀態。 在本範例中，匯入識別碼是 **4eec64dd-533b-47c3-a72c-6508ad854659**。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

以下是使用此匯入識別碼要求狀態：

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

如果工作未完成，則 HTTP 回應可能像這樣：

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

如果工作已完成，則 HTTP 回應比較可能像這樣：

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>資料來源連線 \(以及資料的多重租用)

即使幾乎 .pbix 檔案中的所有成品都會匯入到我們的工作區，但不會包含資料來源的認證。 如此一來，當使用 [DirectQuery 模式] 時，內嵌的報表會無法正確顯示。 但是，當使用 [匯入模式] 時，我們可以使用現有的匯入資料檢視報表。 在這種情況下，我們必須使用下列步驟，透過 REST 呼叫來設定認證。

首先，我們必須取得閘道器資料來源。 我們知道資料集 **id** 是先前傳回的識別碼。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

使用傳回的閘道器識別碼和資料來源識別碼 \(請參閱之前的 **gatewayId** 和所傳回結果的 **id**)，我們可以變更這個資料來源的認證，如下所示：

**HTTP 要求**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP 回應**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

在生產環境中，我們也可以使用 REST API 針對每個工作區設定不同的連接字串。 \(也就是可以針對每個客戶分隔資料庫。)

接著透過 REST 來變更資料來源的連接字串。

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

或者，我們可以使用 Power BI Embedded 中的「資料列層級安全性」，並且可以在單一報表中為每位使用者分隔資料。 如此一來，我們就可以使用同一個 .pbix \(UI 等等) 和不同的資料來源，來佈建每個客戶報表。

> [!NOTE]
> 如果您使用 [匯入模式] 而不是 [DirectQuery 模式]，則無法透過 API 重新整理模型。 而且，Power BI Embedded 尚未支援透過 Power BI 閘道器內部部署資料來源。 不過，建議您持續留意 [Power BI 部落格](https://powerbi.microsoft.com/blog/) ，以了解最新消息和未來版本中將推出的新功能。

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>在我們的網頁中驗證和裝載 (內嵌) 報表

在先前的 REST API 中，我們可以使用存取金鑰 **AppKey** 本身作為授權標頭。 因為這類呼叫可以在後端伺服器端處理，因此非常安全。

不過，當我們在網頁中內嵌報表時，會使用 JavaScript \(前端) 來處理這類安全性資訊。 接著必須保護授權標頭值。 如果我們的存取金鑰被惡意使用者或惡意程式碼發現，他們就可以使用這個金鑰呼叫任何作業。

當我們在網頁中內嵌報表時，我們必須改用已處理的權杖，而不使用存取金鑰 **AppKey**。 我們的應用程式必須建立 OAuth Json Web 權杖 \(JWT)，這是由宣告和已處理的數位簽章所組成。 這個 OAuth JWT 是使用點分隔符號編碼的字串權杖，如下圖所示。

![](media/power-bi-embedded-iframe/oauth-jwt.png)

首先，我們必須準備輸入值，稍後會簽署這個值。 這個值是下列 json 的 base64 url 編碼 (rfc4648) 字串，以點 \(dot) 字元分隔。 稍後，我們會說明如何取得報表識別碼。

> [!NOTE]
> 如果我們想要使用 Power BI Embedded 的資料列層級安全性 (RLS)，則我們在宣告中也必須指定 **username** 和 **roles**。

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

接下來，我們必須使用 SHA&256; 演算法建立 HMAC \(簽章) 的 base64 編碼字串。 這個經過簽署的輸入值是之前的字串。

最後，我們必須使用句號 \(.) 字元結合輸入值和簽章字串。 完整的字串是用於內嵌報表的應用程式權杖。 即使應用程式權杖被惡意使用者發現，他們也無法取得原始的存取金鑰。 此應用程式權杖很快就會到期。

以下是這些步驟的 PHP 範例：

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>最後，將報表內嵌到網頁

如需內嵌我們的報表，我們必須使用下列 REST API 取得內嵌 URL 和報表 **id** 。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

我們可以使用之前的應用程式權杖在 Web 應用程式中內嵌報表。
如果我們查看下一個範例程式碼，會發現前半部與之前的範例相同。 在後半部中，這個範例會在 iframe 中顯示 **embedUrl** \(請參閱之前的結果)，並將應用程式權杖張貼到 iframe 中。

> [!NOTE]
> 您必須將報表識別碼值變更為您擁有的其中一個。 此外，由於我們內容管理系統中的錯誤，程式碼範例中的 iframe 標籤會照字面讀出。 如果您將這個範例程式碼複製並貼上，請移除標籤中的大寫文字。

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

我們的結果如下︰

![](media/power-bi-embedded-iframe/view-report.png)

此時，Power BI Embedded 僅會在 iframe 中顯示報表。 但是，請持續關注 [Power BI 部落格](https://powerbi.microsoft.com/blog/)。 未來的增強功能可能使用新的用戶端 API，讓我們可以傳送資訊到 iframe 以及取出資訊。 令人興奮吧！

## <a name="see-also"></a>另請參閱
* [在 Power BI Embedded 中驗證和授權](power-bi-embedded-app-token-flow.md)

有其他疑問？ [試用 Power BI 社群](http://community.powerbi.com/)


