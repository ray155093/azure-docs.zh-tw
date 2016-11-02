<properties
    pageTitle="使用 Machine Learning Web 服務 | Microsoft Azure"
    description="部署機器學習服務之後，就可以使用 RESTFul Web 服務做為要求-回應服務或批次執行服務。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="garye" />



# <a name="how-to-consume-an-azure-machine-learning-web-service-that-has-been-deployed-from-a-machine-learning-experiment"></a>如何使用已從機器學習實驗部署的 Azure Machine Learning Web 服務

## <a name="introduction"></a>簡介

部署為 Web 服務時，Azure Machine Learning 實驗所提供的 REST API 和 JSON 格式化訊息，可供各種裝置和平台使用。 Azure Machine Learning 入口網站提供的程式碼可用來呼叫 R、C# 和 Python 的 Web 服務。 

要能夠使用任何程式設計語言並從任何裝置呼叫這些服務，必須滿足三個準則：

* 具備網路連線
* 具備執行 HTTPS 要求的 SSL 功能
* 可以剖析 JSON (藉由手動或支援程式庫)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

使用 Azure Machine Learning Web 服務有兩種方式，可以做為要求-回應服務或批次執行服務。 在這兩個案例中，一旦部署實驗後，就可以使用透過 RESTFul Web 服務提供的功能。

> [AZURE.TIP] 如需建立 Web 應用程式以存取您的預測 Web 服務的簡單方式，請參閱 [使用 Azure Machine Learning Web 服務與 Web 應用程式範本](machine-learning-consume-web-service-with-web-app-template.md)。

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

如需如何建立及部署 Azure Machine Learning Web 服務的相關資訊，請參閱[部署 Azure Machine Learning Web 服務][publish]。 如需建立機器學習實驗和部署實驗的逐步解說，請參閱[使用 Azure Machine Learning 開發預測解決方案][walkthrough]。

## <a name="request-response-service-(rrs)"></a>要求回應服務 (RRS)

「要求回應服務 (RRS)」是一種低延遲、高度可調整的 Web 服務，可用來為從 Azure Machine Learning Studio 實驗建立並部署的無狀態模型提供介面。 此服務能讓消費端應用程式要求即時回應。

RRS 可接受輸入參數的單一資料列或多個資料列，且可產生單一資料列或多個資料列做為輸出。 輸出資料列可包含多個資料行。

RRS 範例驗證應用程式的真確性。 您可以將絕大多數應用程式安裝為此種狀況。 當應用程式啟動時，它會使用相關輸入呼叫 RRS 服務。 然後應用程式會接收來自服務的驗證回應，允許或封鎖應用程式執行。


## <a name="batch-execution-service-(bes)"></a>批次執行服務 (BES)

「批次執行服務 (BES)」是一種可為大量的一批資料記錄進行非同步計分的服務。 BES 的輸入包含來自各種來源 (例如 blob、Azure 中的表格、SQL Azure、HDInsight (例如 Hive 查詢的結果) 和 HTTP 來源) 的一批記錄。 BES 的輸出則包含計分的結果。 結果會輸出至位於 Azure blob 儲存體中的檔案，並在回應中傳回儲存體端點的資料。

當您不需要立即收到回應時 (例如為個人或物聯網 (IOT) 裝置定期排程計分)，BES 就很有用。

## <a name="examples"></a>範例

為了同時顯示 RRS 和 BES 兩者如何運作，我們使用 Azure Web 服務範例。 此服務將用於 IOT (Internet Of Things) 案例。 為了簡單起見，我們的裝置只會傳送一個值， `cog_speed`，並取得單一的回應。

部署實驗後，我們需要四種資訊才能呼叫 RRS 或 BES 服務。

* 服務 **API 金鑰**或**金鑰**
* 服務**要求 URI**
* 預期的 API **要求標頭**和**主體**
* 預期的 API **回應標頭**和**主體**

您用來尋找此資訊的方式取決於您部署的服務類型︰新的 Web 服務或傳統 Web 服務。

### <a name="information-location-in-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning Web 服務入口網站中的資訊位置 

若要尋找所需的資訊︰

1. 登入 [Azure Machine Learning Web 服務][webservicesportal]入口網站。
2. 按一下 [Web 服務] 或 [傳統 Web 服務]。
3. 按一下您所使用的 Web 服務。 
4. 如果您使用傳統 Web 服務，請按一下您所使用的端點。

此資訊位於下列頁面︰

* **主要金鑰**可在 [取用] 頁面取得
* **要求 URI** 可在 [取用] 頁面取得 
* 預期的 API **要求標頭**、**回應標頭**和**主體** 可在 [Swagger API] 頁面取得

### <a name="information-locations-in-machine-learning-studio-(classic-web-service-only)"></a>Machine Learning Studio 中的資訊位置 (僅限傳統 Web 服務)

您可以從兩個位置找到所需的資訊︰Machine Learning Studio 或 Azure Machine Learning Web 服務入口網站。

若要在 Machine Learning Studio 中尋找所需的資訊︰

1. 登入 [Machine Learning Studio][mlstudio]。
2. 在畫面的左側，按一下 [WEB 服務]。
3. 按一下您所使用的 Web 服務。 

此資訊位於下列頁面︰

* **API 金鑰**可在服務**儀表板**取得 
* **要求 URI** 可在 API 說明頁面取得
* 預期的 API **要求標頭**、**回應標頭**和**主體** 可在 API 說明頁面取得


若要存取 API 說明頁面，請視您的工作情況按一下 [要求/回應] 或 [批次執行] 連結。

若要在 Azure Machine Learning Web 服務入口網站上尋找所需的資訊︰

1. 登入 [Azure Machine Learning Web 服務][webservicesportal]入口網站。
2. 按一下 [傳統 Web 服務]。
3. 按一下您所使用的 Web 服務。 
4. 按一下您所使用的端點。

此資訊位於下列頁面︰

* **主要金鑰**可在 [取用] 頁面取得
* **要求 URI** 可在 [取用] 頁面取得 
* 預期的 API **要求標頭**、**回應標頭**和**主體** 可在 [Swagger API] 頁面取得

在下列兩個範例中，我們使用 C# 語言說明所需的程式碼。

### <a name="rrs-example"></a>RRS 範例



下列範例要求顯示 API 輸入，也就是我們的範例服務的 API 呼叫承載。 對於傳統 Web 服務，您可以在 **API 說明頁面**或在 Machine Learning Web 服務入口網站的 [Swagger API] 頁面上找到承載範例。 對於新的 Web 服務，您可以在 Machine Learning Web 服務入口網站的 [Swagger API] 頁面上找到承載範例。

**範例要求**

    {
      "Inputs": {
        "input1": {
          "ColumnNames": [
            "cog_speed"
          ],
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }


同樣地，下列範例顯示此服務的 API 回應。

**範例回應**

    {
      "Results": {
        "output1": {
          "type": "DataTable",
          "value": {
            "ColumnNames": [
              "cog_speed"
            ],
            "ColumnTypes": [
              "Numeric"
            ].
          "Values": [
            [
              "0"
            ],
            [
              "1"
            ]
          ]
        }
      },
      "GlobalParameters": {}
    }

以下是 C# 實作的程式碼範例。 對於傳統 Web 服務，您可以在 **API 說明頁面**的底部或 [取用] 頁面的底部找到程式碼範例。 對於新的 Web 服務，您可以在 [取用] 頁面的底部找到程式碼範例。

**C# 的範例程式碼**

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Formatting;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;

    namespace CallRequestResponseService
    {
        public class StringTable
        {
            public string[] ColumnNames { get; set; }
            public string[,] Values { get; set; }
        }

        class Program
        {
            static void Main(string[] args)
            {
                InvokeRequestResponseService().Wait();
            }

            static async Task InvokeRequestResponseService()
            {
                using (var client = new HttpClient())
                {
                    var scoreRequest = new
                    {
                        Inputs = new Dictionary<string, StringTable> () {
                            {
                                "input1",
                                new StringTable()
                                {
                                    ColumnNames = new string[] {"cog_speed"},
                                    Values = new string[,] {  { "0"},  { "1"}  }
                                }
                            },
                        GlobalParameters = new Dictionary<string, string>() { }
                    };

                    const string apiKey = "abc123"; // Replace this with the API key for the Web service
                    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);

                    client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");

                    // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
                    // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
                    // For instance, replace code such as:
                    //      result = await DoSomeTask()
                    // with the following:
                    //      result = await DoSomeTask().ConfigureAwait(false)

                    HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                    if (response.IsSuccessStatusCode)
                    {
                        string result = await response.Content.ReadAsStringAsync();
                        Console.WriteLine("Result: {0}", result);
                    }
                    else
                    {
                        Console.WriteLine("Failed with status code: {0}", response.StatusCode);
                    }
                }
            }
        }
    }

**Java 的範例程式碼**

下列範例程式碼示範如何在 Java 中建構 REST API 要求。 它假設變數 (apikey 和 apiurl) 包含必要的 API 詳細資料，且 jsonBody 變數包含如 REST API 所預期的正確 JSON 物件。 您可以從 github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp) 下載完整的程式碼。 此 Java 範例需要使用 [apache http 用戶端程式庫](https://hc.apache.org/downloads.cgi)。

    /**
     * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
     */
        /**
          * Call REST API for retrieving prediction from Azure ML 
          * @return response from the REST API
          */    
        public static String rrsHttpPost() {
        
            HttpPost post;
            HttpClient client;
            StringEntity entity;
        
            try {
                    // create HttpPost and HttpClient object
                    post = new HttpPost(apiurl);
                    client = HttpClientBuilder.create().build();
            
                    // setup output message by copying JSON body into 
                    // apache StringEntity object along with content type
                    entity = new StringEntity(jsonBody, HTTP.UTF_8);
                    entity.setContentEncoding(HTTP.UTF_8);
                    entity.setContentType("text/json");

                    // add HTTP headers
                    post.setHeader("Accept", "text/json");
                    post.setHeader("Accept-Charset", "UTF-8");
        
                    // set Authorization header based on the API key
                    post.setHeader("Authorization", ("Bearer "+apikey));
                    post.setEntity(entity);

                    // Call REST API and retrieve response content
                    HttpResponse authResponse = client.execute(post);
            
                    return EntityUtils.toString(authResponse.getEntity());
            
            }
            catch (Exception e) {
            
                    return e.toString();
            }
    
        }
    
        
 

### <a name="bes-example"></a>BES 範例

與 RRS 服務不同，BES 服務是非同步的。 這表示 BES API 只是將要執行的工作排入佇列，而呼叫者會輪詢工作的狀態以查看工作何時完成。 以下是目前支援的批次工作作業：

1. 建立 (提交) 批次工作
1. 啟動此批次工作
1. 取得批次工作的狀態或結果
1. 取消執行中的批次工作

**1.建立批次執行工作**

建立 Azure Machine Learning 服務的批次作業時，您可以指定可定義批次執行的數個參數：

* **Input**：代表儲存批次作業輸入所在位置的 Blob 參考。
* **GlobalParameters**：代表您可針對實驗定義的一組全域參數。 Azure Machine Learning 實驗可以具有必要和選用參數，以自訂服務的執行，而呼叫者預期會提供所有必要的參數 (如果有這些參數)。 這些參數會以索引鍵-值組的集合形式來指定。
* **Outputs**：如果服務已定義一或多個輸出，則呼叫端可將任一輸出重新導向到某個 Azure Blob 位置。 設定此參數，您即可以可預測的名稱，將服務的輸出儲存在慣用位置，否則會隨機產生輸出 Blob 名稱。 

    服務預期輸出內容 (依其類型) 會儲存為支援的格式：
  - 資料集輸出：可以儲存為 **.csv、.tsv、.arff**
  - 定型模型輸出：必須儲存為 **.ilearner**

    您可將輸出位置覆寫指定為輸出名稱或 Blob 參考組的集合。 「輸出名稱」 是特定輸出節點的使用者定義名稱，而「Blob 參考」則是 Azure Blob 位置的參考，也就是輸出要重新導向的位置。 「輸出名稱」服務的 API 說明頁面上。

視您的服務性質而定，所有作業建立參數均可選擇性地使用。 例如，沒有定義輸入節點的服務不需要傳入 *Input* 參數。 同樣地，輸出位置覆寫功能為選擇性功能，因為如果不使用，輸出也會儲存在您針對 Azure Machine Learning 工作區所設定的預設儲存體帳戶中。 以下是針對只提供輸入資訊的服務所示範的要求承載：

**範例要求**

    {
      "Input": {
        "ConnectionString":     
        "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
        "RelativeLocation": "/mycontainer/mydatablob.csv",
        "BaseLocation": null,
        "SasBlobToken": null
      },
      "Outputs": null,
      "GlobalParameters": null
    }

批次作業建立 API 的回應是與您作業相關聯的唯一作業 ID。 這個 ID 很重要，因為它是讓您針對其他作業參考系統中此作業的唯一方法。  

**範例回應**

    "539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2.啟動批次執行工作**

當您建立批次作業時，它會在系統內註冊並呈現「未啟動」狀態。 若要實際排程作業以便執行，請呼叫服務端點的 API 說明頁面或 Web 服務的 Swagger API 頁面上所述的 **start** API，並提供建立作業時所取得的作業 ID。

**3.取得批次執行工作的狀態**

您可以隨時將作業 ID 傳遞至 *GetJobStatus* API，藉此輪詢非同步批次作業的狀態。 API 回應包含作業目前狀態的指標，以及批次作業的結果 (指出該作業是否順利完成)。 如果發生錯誤，則會在 Details 屬性中傳回失敗背後的實際原因詳細資訊，如下所示：

**回應承載**

    {
        "StatusCode": STATUS_CODE,
        "Results": RESULTS,
        "Details": DETAILS
    }

*StatusCode* 可以是下列其中一項：

* 未啟動
* 執行中
* Failed
* Canceled
* 已完成

*Results* 屬性會在順利完成作業時填入 (否則它會是 **null** )。作業完成後，且服務至少定義一個輸出節點時，傳回的結果會是 [輸出名稱, Blob 參考]  組的集合，其中的 Blob 參考是包含結果之 Blob 的 SAS 唯讀參考。

**範例回應**

    {
        "Status Code": "Finished",
        "Results":
        {
            "dataOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "outputs/dataOutput.csv",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },
            "trainedModelOutput":
            {              
                "ConnectionString": null,
                "RelativeLocation": "models/trainedModel.ilearner",
                "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
                "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
            },           
        },
        "Details": null
    }

**4.取消批次執行工作**

呼叫指定的 *CancelJob* API 並傳入作業 ID，即可隨時取消執行中的批次作業。 您可能會基於各種原因而進行取消，例如作業花太長時間而無法完成。

#### <a name="using-the-bes-sdk"></a>使用 BES SDK

[BES SDK Nuget 套件](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/)提供可以批次模式簡化呼叫 BES 來進行評分的功能。 若要安裝 Nuget 套件，請在 Visual Studio 的 [工具] 功能表中，選取 [Nuget 套件管理員]，然後按一下 [套件管理員主控台]。

Azure Machine Learning 實驗已部署為可包含 Web 服務輸入模型的 Web 服務。 這表示 Web 服務輸入是以 Blob 位置參考的形式透過 Web 服務來提供。 您也可以選擇不使用 Web 服務輸入模組，而改用「匯入資料」模組。 在此情況下，「匯入資料」模組會在執行階段使用查詢從資料來源 (例如 SQL DB) 進行讀取。 Web 服務參數可用來動態指向其他伺服器或資料表等。SDK 支援以上兩種模式。

下列程式碼範例示範如何使用 BES SDK，針對 Azure Machine Learning 服務提交和監視批次作業。 註解包含設定和呼叫的詳細資訊。

#### <a name="**sample-code**"></a>**範例程式碼**

    // This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
    // Instructions for doing this in Visual Studio:
    // Tools -> Nuget Package Manager -> Package Manager Console
    // Install-Package Microsoft.Azure.MachineLearning

      using System;
      using System.Collections.Generic;
      using System.Threading.Tasks;

      using Microsoft.Azure.MachineLearning;
      using Microsoft.Azure.MachineLearning.Contracts;
      using Microsoft.Azure.MachineLearning.Exceptions;

    namespace CallBatchExecutionService
    {
        class Program
        {
            static void Main(string[] args)
            {               
                InvokeBatchExecutionService().Wait();
            }

            static async Task InvokeBatchExecutionService()
            {
                // First collect and fill in the URI and access key for your Web service endpoint.
                // These are available on your service's API help page.
                var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
                string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

                // Create an Azure Machine Learning runtime client for this endpoint
                var runtimeClient = new RuntimeClient(endpointUri, accessKey);

                // Define the request information for your batch job. This information can contain:
                // -- A reference to the AzureBlob containing the input for your job run
                // -- A set of values for global parameters defined as part of your experiment and service
                // -- A set of output blob locations that allow you to redirect the job's results

                // NOTE: This sample is applicable, as is, for a service with explicit input port and
                // potential global parameters. Also, we choose to also demo how you could override the
                // location of one of the output blobs that could be generated by your service. You might
                // need to tweak these features to adjust the sample to your service.
                //
                // All of these properties of a BatchJobRequest shown below can be optional, depending on
                // your service, so it is not required to specify all with any request.  If you do not want to
                // use any of the parameters, a null value should be passed in its place.

                // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

                // If applicable, you can also set the global parameters for your service
                var globalParameters = new Dictionary<string, string>
                {
                    { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
                };

                var jobRequest = new BatchJobRequest
                {
                    Input = inputBlob,
                    GlobalParameters = globalParameters,
                    Outputs = outputLocations
                };

                try
                {
                    // Register the batch job with the system, which will grant you access to a job object
                    BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

                    // Start the job to allow it to be scheduled in the running queue
                    await job.StartAsync();

                    // Wait for the job's completion and handle the output
                    BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
                    if (jobStatus.JobState == JobState.Finished)
                    {
                        // Process job outputs
                        Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
                        foreach (var output in jobStatus.Results)
                        {
                            Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
                        }
                    }
                    else if (jobStatus.JobState == JobState.Failed)
                    {
                        // Handle job failure
                        Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
                    }
                }
                catch (ArgumentException aex)
                {
                    Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
                }
                catch (RuntimeException runtimeError)
                {
                    Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
                    Console.WriteLine("Error details:");
                    foreach (var errorDetails in runtimeError.Details)
                    {
                        Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
                }
            }
        }
    }

#### <a name="sample-code-in-java-for-bes"></a>Java 中適用於 BES 的範例程式碼

批次執行服務 REST API 會採用包含輸入範例 csv 和輸出範例 csv 的參考的 JSON (如下列範例所示)，並在 Azure ML 中建立作業以執行批次預測。 您可以在 [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp)中檢視完整的程式碼。 此 Java 範例需要使用 [apache http 用戶端程式庫](https://hc.apache.org/downloads.cgi)。 


    { "GlobalParameters": {}, 
        "Inputs": { "input1": { "ConnectionString":     "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=Q8kkieg==", 
            "RelativeLocation": "myContainer/sampleinput.csv" } }, 
        "Outputs": { "output1": { "ConnectionString":   "DefaultEndpointsProtocol=https;
            AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
            "RelativeLocation": "myContainer/sampleoutput.csv" } } 
    } 


##### <a name="create-a-bes-job"></a>建立 BES 工作  
        
        /**
         * Call REST API to create a job to Azure ML 
         * for batch predictions
         * @return response from the REST API
         */ 
        public static String besCreateJob() {
            
            HttpPost post;
            HttpClient client;
            StringEntity entity;
            
            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(apiurl);
                client = HttpClientBuilder.create().build();
                
                // setup output message by copying JSON body into 
                // apache StringEntity object along with content type
                entity = new StringEntity(jsonBody, HTTP.UTF_8);
                entity.setContentEncoding(HTTP.UTF_8);
                entity.setContentType("text/json");
    
                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");
            
                // set Authorization header based on the API key
                // note a space after the word "Bearer " - don't miss that
                post.setHeader("Authorization", ("Bearer "+apikey));
                post.setEntity(entity);
    
                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);
                
                jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll("\"", "");
                
                
                return jobId;
                
            }
            catch (Exception e) {
                
                return e.toString();
            }
        
        }
        
##### <a name="start-a-previously-created-bes-job"></a>啟動先前建立的 BES 工作        
    
        /**
         * Call REST API for starting prediction job previously submitted 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */ 
        public static String besStartJob(String job){
            HttpPost post;
            HttpClient client;
            StringEntity entity;
            
            try {
                // create HttpPost and HttpClient object
                post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
                client = HttpClientBuilder.create().build();
             
                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");
            
                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));
    
                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);
                
                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                
                return EntityUtils.toString(authResponse.getEntity());
                
            }
            catch (Exception e) {
                
                return e.toString();
            }
        }
##### <a name="cancel-a-previously-created-bes-job"></a>取消先前建立的 BES 工作
        
        /**
         * Call REST API for canceling the batch job 
         * 
         * @param job job to be started 
         * @return response from the REST API
         */ 
        public static String besCancelJob(String job) {
            HttpDelete post;
            HttpClient client;
            StringEntity entity;
            
            try {
                // create HttpPost and HttpClient object
                post = new HttpDelete(startJobUrl+job);
                client = HttpClientBuilder.create().build();
             
                // add HTTP headers
                post.setHeader("Accept", "text/json");
                post.setHeader("Accept-Charset", "UTF-8");
            
                // set Authorization header based on the API key
                post.setHeader("Authorization", ("Bearer "+apikey));
    
                // Call REST API and retrieve response content
                HttpResponse authResponse = client.execute(post);
             
                if (authResponse.getEntity()==null)
                {
                    return authResponse.getStatusLine().toString();
                }
                return EntityUtils.toString(authResponse.getEntity());
                
            }
            catch (Exception e) {
                
                return e.toString();
            }
        }
        
### <a name="other-programming-environments"></a>其他程式設計環境

您也可以依照 [swagger.io](http://swagger.io/) 網站所提供的指示，產生其他許多種語言的程式碼。 對於傳統 Web 服務，您可以取得 swagger 文件︰

* 從 API 說明頁面 
* 藉由呼叫 Get API Document for Endpoint (可在 Machine Learning Web 服務入口網站的 [Swagger API] 頁面上找到)。 

前往 [swagger.io](http://swagger.io/swagger-codegen/)，並依照指示下載 Swagger 程式碼、java 及 apache mvn。 

以下清單是針對其他程式設計環境設定 Swagger 的指示的摘要。

* 請確定已安裝 Java 7 或更新版本
* 安裝 apache mvn (在 ubuntu 上，您可以使用 *apt-get install mvn*)
* 前往 github 中的 swagger，並將 Swagger 專案下載為 zip 檔案
* 解壓縮 Swagger
* 從 Swagger 的來源目錄執行「mvn 封裝」  ，以建置 Swagger 工具

現在，您可以使用任何 Swagger 工具。 以下是產生 Java 用戶端程式碼的指示。 

* 前往 Azure ML API 說明頁面 (範例在 [這裡](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs))
* 為 Azure ML REST API 尋找 swagger.json 的 URL (在 API 說明頁面最上方的倒數第二個項目符號)
* 按一下 Swagger 文件連結 (範例在 [這裡](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument))
* 使用 [Swagger 讀我檔案](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) 中所示的下列命令產生用戶端程式碼

**產生用戶端程式碼的範例命令列**

    java -jar swagger-codegen-cli.jar generate\
     -i https://ussouthcentral.services.azureml.net:443/workspaces/\
    fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
     -l java -o /home/username/sample

* 在如下所示的 Swagger [API 說明頁面](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument) 範例中，結合 host、basePath 和 "/swagger.json" 欄位中的值來建構上述命令列中使用的 Swagger URL。

**範例 API 說明頁面**


    {
      "swagger": "2.0",
      "info": {
        "version": "2.0",
        "title": "Sample 5: Binary Classification with Web service: Adult Dataset [Predictive Exp.]",
        "description": "No description provided for this Web service.",
        "x-endpoint-name": "default"
      },
      "host": "ussouthcentral.services.azureml.net:443",
      "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
      "schemes": [
        "https"
      ],
      "consumes": [
        "application/json"
      ],
      "produces": [
        "application/json"
      ],
      "paths": {
        "/swagger.json": {
          "get": {
            "summary": "Get swagger API document for the Web service",
            "operationId": "getSwaggerDocument",
            
<!-- Relative Links -->

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- External Links -->
[webservicesportal]: https://services.azureml.net/
[mlstudio]: https://studio.azureml.net


<!--HONumber=Oct16_HO2-->


