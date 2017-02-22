---
title: "將資料匯入 Azure Application Insights 中的分析 | Microsoft Docs"
description: "匯入靜態資料以加入應用程式遙測，或匯入個別的資料流以分析查詢。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 47c3491b067d5e112db589672b68e7cfc7cbe921
ms.openlocfilehash: eb89c6f485f2321f729dcfe650af4355de84a9ac


---
# <a name="import-data-into-analytics"></a>將資料匯入分析

將任何表格式資料匯入[分析](app-insights-analytics.md)，將它從應用程式加入 [Application Insights](app-insights-overview.md) 遙測，或是讓您可以為個別的資料流進行分析。 分析是適用於分析大量遙測時間戳記資料流的強大查詢語言。

您可以使用自己的結構描述將資料匯入分析。 它不需要使用標準 Application Insights 結構描述，例如要求或追蹤。

目前，您可以匯入 CSV (逗點分隔值) 檔案，或使用 tab 或分號分隔符號的類似格式。

有三種情況下，匯入至分析會很實用︰

* **加入應用程式遙測。** 例如，您可以匯入將來自您網站的 URL 對應至更容易閱讀之頁面標題的資料表。 在分析中，您可以建立儀表板圖表報表，其會顯示您的網站中十個最常用的頁面。 現在其可以顯示頁面標題，而非 URL。
* **相互關聯應用程式遙測**與其他來源，例如網路流量、伺服器資料或 CDN 記錄檔。
* **將個別的資料流套用至分析。** Application Insights 分析是強大的工具，可搭配疏鬆、時間戳記資料流 - 在許多情況下遠優於 SQL。 如果您有來自其他來源的資料流，您可以使用分析進行分析。

將資料傳送至您的資料來源很簡單。 

1. (一次性) 在資料來源中定義資料結構描述。
2. (定期) 將您的資料上傳至 Azure 儲存體並呼叫 REST API，以通知我們新的資料正在等候擷取。 在幾分鐘內，資料可在分析中供查詢。

上傳的頻率是由您以及您希望您的資料多快可供查詢來定義。 以較大的區塊上傳是更有效率的方式，但不超過 1 GB。

> [!NOTE]
> *有許多資料來源要分析嗎？* [*請考慮使用 logstash **以將您的資料傳送至 Application Insights。*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>開始之前

您需要：

1. Microsoft Azure 中的 Application Insights 資源。

 * 如果您想要與其他遙測分開分析您的資料，[建立新的 Application Insights 資源](app-insights-create-new-resource.md)。
 * 如果您要加入或比較從已使用 Application Insights 設定的應用程式遙測之資料，您可以使用該應用程式的資源。
 * 參與者或擁有者存取該資源。
 
2. Azure 儲存體中的 Blob。 您上傳至 Azure 儲存體，分析會從該處取得您的資料。 

 * 我們建議您針對 blob 建立專用的儲存體帳戶。 如果與其他處理序共用您的 blob，我們的程序會花較長時間來讀取 blob。

2. 這項功能在預覽時，您必須要求進行存取。

 * 從 [Azure 入口網站](https://portal.azure.com)中的 Application Insights 資源，開啟分析。 
 * 在 [結構描述] 窗格的底部，按一下 [其他資料來源] 下方的 [與我們連絡] 連結。 
 * 如果您看到「新增資料來源」，則您已經有存取權。


## <a name="define-your-schema"></a>定義結構描述

在您匯入資料之前，您必須定義資料來源，以指定您資料的結構描述。

1. 啟動資料來源精靈

    ![新增資料來源](./media/app-insights-analytics-import/add-new-data-source.png)

2. 依照指示上傳範例資料檔案。

 * 此範例的第一列可以是資料行標頭。 (您可以在下一個步驟中變更欄位名稱。)
 * 範例應該包含至少 10 個資料列的資料。

3. 檢閱精靈已經從您的範例推斷之結構描述。 如有必要，您可以調整資料行的推斷類型。

4. 選取時間戳記。 分析中的所有資料都必須都有時間戳記欄位。 必須有 `datetime` 類型，但不必命名為 'timestamp'。 如果您的資料具有包含以 ISO 格式表示之日期和時間的資料行，選擇此選項為時間戳記資料行。 否則，請選擇「資料抵達時」，匯入程序將會新增時間戳記欄位。

    ![檢閱結構描述](./media/app-insights-analytics-import/data-source-review-schema.png)

5. 建立資料來源。


## <a name="import-data"></a>匯入資料

若要匯入資料，請將它上傳至 Azure 儲存體、建立便捷鍵，然後建立 REST API 呼叫。

![新增資料來源](./media/app-insights-analytics-import/analytics-upload-process.png)

您可以手動執行下列程序，或設定自動系統定期執行。 您必須依照下列步驟進行每個您要匯入的資料區塊。

1. 將資料上傳至 [Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。 

 * Blob 未壓縮大小可以達 1 GB 。 從效能觀點來看，數百 MB 的大型 blob 很適合。
 * 可以使用 Gzip 進行壓縮，以改善上傳時間和延遲時間，以便資料可用於查詢。 使用 `.gz` 副檔名。
 
2. [建立 blob 的共用存取簽章](../storage/storage-dotnet-shared-access-signature-part-2.md). 金鑰應該有一天的到期時間，並提供讀取權限。
3. 進行 REST 呼叫，以通知 Application Insights 資料正在等候。

 * 端點：`https://dc.services.visualstudio.com/v2/track`
 * HTTP 方法：POST
 * 承載：

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

預留位置是︰

* `Blob URI with Shared Access Key`︰從建立機碼程序取得。 它是 blob 特定。
* `Schema ID`：為已定義的結構描述產生的結構描述識別碼。 此 Blob 中的資料應該與結構描述相符。
* `DateTime`：送出要求的時間 (UTC)。 我們接受這些格式︰ISO8601 (例如 "2016-01-01 13:45:01")；RFC822 ("Wed, 14 Dec 16 14:57:01 +0000")；RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC")；RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000")。
* 您 `Instrumentation key` 的 Application Insights 資源。

後幾分鐘可在分析中使用資料。

## <a name="error-responses"></a>錯誤回應

* **400 不正確的要求**︰表示要求承載無效。 勾選：
 * 修正動態檢測金鑰。
 * 有效的時間值。 它應該是現在的 UTC 時間。
 * 資料符合結構描述。
* **403 禁止**︰您所傳送的 blob 不能存取。 請確定共用的存取金鑰有效且尚未過期。
* **404 找不到**：
 * Blob 不存在。
 * 資料來源名稱錯誤。

回應錯誤訊息中有更詳細的資訊。

## <a name="sample-code"></a>範例程式碼

此程式碼使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet 封裝。

### <a name="classes"></a>類別

```C#


namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            HttpWebResponse response; 
            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 

```

### <a name="ingest-data"></a>擷取資料

針對每個 blob 使用此程式碼。 

```C#


   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "tableId/sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);

```

## <a name="next-steps"></a>後續步驟

* [分析查詢語言導覽](app-insights-analytics-tour.md)
* [使用 Logstash 將資料傳送至 Application Insights](https://github.com/Microsoft/logstash-output-application-insights)



<!--HONumber=Jan17_HO3-->


