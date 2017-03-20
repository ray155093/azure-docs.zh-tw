---
title: "使用封包擷取搭配 Azure Functions 進行主動式網路監視 | Microsoft Docs"
description: "本文說明如何使用 Azure 網路監看員建立警示觸發的封包擷取"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>使用封包擷取搭配 Azure Functions 進行主動式網路監視

網路監看員封包擷取可建立擷取工作階段來追蹤虛擬機器的流入和流出流量。 擷取檔案可以有定義來只追蹤您想要監視之流量的篩選。 接著，此資料會儲存在儲存體 blob 或本機客體機器上。 這項功能可以從其他的自動化案例遠端啟動，例如 Azure Functions。 封包擷取會根據定義的網路異常狀況提供執行主動擷取的功能。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。

在 Azure 中部署的資源為 24/7 執行。 您或您的人員無法 24/7 主動監視所有資源的狀態。 如果凌晨 2 點發生問題，會發生什麼事？

藉由使用 Azure 生態系統內的網路監看員、警示及函式，您可以主動使用資料和工具回應網路中的問題以解決問題。

## <a name="before-you-begin"></a>開始之前

在此範例中，您的 VM 正在比平常傳送更多個 TCP 區段，且您想要接獲通知。 會使用 TCP 區段做為範例，您可以使用任何警示的條件。 當您收到警示時，您需要有封包層級的資料，以了解通訊增加的原因，以便您可以採取步驟，讓機器回復到一般的通訊。
此案例假設您有現有的網路監看員執行個體，並且具有可供使用的有效虛擬機器的資源群組。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>案例

若要自動化此程序，我們會在 VM 上建立並連線警示，以在事件發生時觸發，以及呼叫網路監看員的 Azure 函式。

此案例將會：

* 建立啟動封包擷取的 Azure 函式。
* 在虛擬機器上建立警示規則
* 設定警示規則以便呼叫 Azure 函式

## <a name="creating-an-azure-function-and-overview"></a>建立 Azure 函式與概觀

第一個步驟是建立 Azure 函式來處理警示，以及建立封包擷取。 

下列清單是所進行工作流程的概觀。

1. 會在您的 VM 上觸發警示。
1. 警示會透過 webhook 呼叫您的 Azure 函式。
1. 您的 Azure 函式會處理警示，並啟動網路監看員封包擷取工作階段。
1. 封包擷取會在 VM 上執行並收集流量。 
1. 擷取檔案會上傳至儲存體帳戶以供您檢閱和診斷 

遵循[建立您的第一個 Azure 函式](../azure-functions/functions-create-first-azure-function.md)，可在入口網站中建立 Azure 函式。 此範例中，我們選擇 HttpTrigger-CSharp 類型函式。 

> [!NOTE]
> 會使用 Azure Functions 提供其他語言，但可能是實驗性且沒有完整的支援，像是 PowerShell 和 Python。

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>處理警示和啟動封包擷取工作階段

現在是時候從 Azure 函式中對網路監看員進行呼叫了。 根據需求，此函式的實作會有所不同。 不過，程式碼的一般流程是這類︰

1. 處理程序輸入參數
2. 查詢現有封包擷取會驗證限制，並且解決名稱衝突
3. 使用適當的參數建立封包擷取
4. 定期輪詢封包擷取直到完成
5. 通知使用者封包擷取工作階段已完成

下列範例是可在 Azure 函式中使用的 C#。 需要針對訂用帳戶、用戶端識別碼、租用戶識別碼和用戶端密碼更換一些值。

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

一旦建立您的函式後，您需要設定您的警示以呼叫與函式相關聯的 URL。 若要取得此值，請從函數應用程式複製函式 URL。

![尋找函式 URL][2]

如果您在 webhook POST 要求的承載中需要自訂屬性，請參閱 [針對 Azure 度量警示設定 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>在 VM 上設定警示

警示可以設定為當特定計量超出指派給它的臨界值時通知個人。 在此範例中，警示會在 TCP 區段上傳送，但可針對許多其他計量觸發警示。 在此範例中，警示會設定為呼叫 webhook 來呼叫函式。

### <a name="create-the-alert-rule"></a>建立警示規則

瀏覽至現有的虛擬機器，並新增警示規則。 如需設定警示相關的詳細文件，請參閱[建立 Azure 服務警示的使用者 Azure 入口網站](../monitoring-and-diagnostics/insights-alerts-portal.md)。 

![新增 VM 警示規則至虛擬機器][1]

> [!NOTE]
> 預設不會啟用計量。 深入了解如何啟用新增計量，請造訪[啟用監視和診斷](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

最後從上一個步驟將 URL 貼製警示的 [webhook] 文字方塊中。 按一下 [確定] 以儲存警示規則。

![將 URL 貼到警示規則][3]

## <a name="downloading-and-viewing-the-capture-file"></a>下載和檢視擷取檔案

如果您將擷取儲存至儲存體帳戶，則擷取檔案可透過入口網站或以程式設計的方式下載。 如果擷取檔案儲存在本機，可藉由登入虛擬機器來擷取檔案。 

如需從 Azure 儲存體帳戶下載檔案的指示，請參閱[以 .NET 開始使用 Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。 另一項可用工具為儲存體總管。 有關儲存體總管的詳細資訊可以在下列連結找到︰[儲存體總管](http://storageexplorer.com/)

一旦下載擷取後，您可以使用可讀取 **.cap** 檔案的任何工具來檢視它。 以下是兩個工具的連結︰

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>後續步驟

瀏覽[使用 Wireshark 的封包擷取分析](network-watcher-alert-triggered-packet-capture.md)，了解如何檢視封包擷取

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

