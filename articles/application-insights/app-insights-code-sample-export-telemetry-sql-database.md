---
title: "範例：剖析從 Azure Application Insights 匯出的資料 | Microsoft Docs"
description: "使用連續匯出功能，在 Application Insights 中自行撰寫遙測資料分析的程式碼。 將資料儲存至 SQL。"
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: fedd078402bbd220bce9b71cd035508d46f92f82


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>程式碼範例：剖析從 Application Insights 匯出的資料
本文說明如何撰寫程式碼，以使用[連續匯出][export]處理從 [Azure Application Insights][start] 匯出的資料。 連續匯出會將您的遙測資料以 JSON 格式移入 Azure 儲存體，因此我們要撰寫一些程式碼來剖析 JSON 物件，並在資料庫資料表中建立資料列。

舉例來說，我們將撰寫程式碼將您的遙測資料從 Application Insights 移到 SQL Database 中。

在您開始之前，請注意：

* 有一個更有效率的方法可將匯出的資料轉移到資料庫中，就是[使用串流分析](app-insights-code-sample-export-sql-stream-analytics.md)，但此處我們的目標是要示範一些可處理所匯出資料的程式碼。 您可以調整這個程式碼範例，以使用匯出的遙測資料執行其他作業。
* 在此範例中，我們會透過以 Azure 背景工作角色執行程式碼，將資料移到 Azure 資料庫中。 但是您可以將此程式碼調整成在內部部署伺服器上執行，以將資料提取到內部部署 SQL 伺服器中。
* 您可以在 Application Insights 中[撰寫程式碼來直接存取遙測](http://dev.applicationinsights.io/)，而不需匯出。

如果您尚未開始使用 Application Insights 監視您的 Web 應用程式，[請立即進行][start]。



## <a name="create-storage-in-azure"></a>在 Azure 中建立儲存體
來自 Application Insights 的資料一律會以 JSON 格式匯出到 Azure 儲存體帳戶。 您的程式碼會讀取這個儲存體中的資料。

1. 在 [Azure 入口網站][portal]的訂用帳戶中建立「傳統」儲存體帳戶。
   
    ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. 建立容器
   
    ![在新的儲存體中，選取 [容器]，按一下容器磚，然後按一下 [新增]](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>啟動對 Azure 儲存體的連續匯出
1. 在 Azure 入口網站中，瀏覽至您為應用程式建立的 Application Insights 資源。
   
    ![依序選擇 [瀏覽]、[Application Insights]、您的應用程式](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. 建立連續匯出。
   
    ![依序選擇 [設定]、[連續匯出]、[新增]](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    選取您稍早建立的儲存體帳戶：

    ![設定匯出目的地](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    設定您想要查看的事件類型：

    ![選擇事件類型](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. 可讓一些資料累積。 請休息一下，讓其他人使用您的應用程式一段時間。 遙測資料會送過來，而您會在[計量瀏覽器](app-insights-metrics-explorer.md)中看到統計圖表，並在[診斷搜尋](app-insights-diagnostic-search.md)中看到個別事件。 
   
    此外，資料會匯出至您的儲存體。 
2. 檢查匯出的資料。 在 Visual Studio 中，依序選擇 [檢視] 和 [Cloud Explorer]，然後依序開啟 [Azure] 和 [儲存體]。 (如果您沒有此功能表選項，您需要安裝 Azure SDK：開啟 [新增專案] 對話方塊，然後開啟 [Visual C#] / [Cloud] / [取得 Microsoft Azure SDK for .NET]。)
   
    ![在 Visual Studio 中，依序開啟 [Server Browser]、[Azure]、[儲存體]](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    記下衍生自應用程式名稱和檢測金鑰之路徑名稱的共同部分。 

事件會以 JSON 格式寫入至 Blob 檔案。 每個檔案可能會包含一或多個事件。 因此我們想要讀取事件資料，並篩選出需要的欄位。 我們可以利用資料執行各種作業，但我們現在打算撰寫一些程式碼，將資料移至 SQL Database。 這麼做可讓您輕鬆執行許多有趣的查詢工作。

## <a name="create-an-azure-sql-database"></a>建立 Azure SQL Database
針對此範例，我們將撰寫程式碼以將資料推送至資料庫。

同樣地，請從您在 [Azure 入口網站][portal]中的訂用帳戶開始，建立您將寫入資料的資料庫 (和一部新伺服器，除非您已經有新伺服器)。

![[新增]、[資料]、[SQL]](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

確定資料庫伺服器允許存取 Azure 服務：

![[瀏覽]、[伺服器]、您的伺服器、[設定]、[防火牆]、[允許存取 Azure]](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>建立背景工作角色
現在，我們終於可以撰寫 [一些程式碼](https://sesitai.codeplex.com/) 來剖析匯出之 Blob 的 JSON，並在資料庫中建立記錄。 因為匯出存放區和資料庫都在 Azure 中，所以我們將在 Azure 背景工作角色中執行程式碼。

此程式碼會自動擷取 JSON 中出現的任何屬性。 如需屬性的描述，請參閱 [匯出資料模型](app-insights-export-data-model.md)。

#### <a name="create-worker-role-project"></a>建立背景工作角色專案
在 Visual Studio 中，建立新的背景工作角色專案：

![[新增專案]、[Visual C#]、[雲端]、[Azure 雲端服務]](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![在新的 [雲端服務] 對話方塊中，依序選擇 [Visual C#]、[背景工作角色]](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>連接到儲存體帳戶
在 Azure 中，取得來自儲存體帳戶的連接字串：

![在 [儲存體帳戶] 中，選取 [索引鍵]，然後複製 [主要連接字串]](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

在 Visual Studio 中，使用儲存體帳戶連接字串設定背景工作角色設定：

![在 [方案總管] 的 [雲端服務] 專案中，展開 [角色]，然後開啟背景工作角色。 開啟 [設定] 索引標籤，選擇 [新增設定]，並設定名稱 = StorageConnectionString，類型 = 連接字串，然後按一下以設定值。 手動設定該數值，並貼上連接字串。](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>封裝
在 [方案總管] 中，以滑鼠右鍵按一下您的背景工作角色專案並選擇 [管理 NuGet 封裝]。
搜尋並安裝這些封裝： 

* EntityFramework 6.1.2 或更新版本 - 根據 Blob 中的 JSON 內容，我們將使用此封裝即時產生資料庫的資料表結構描述。
* JsonFx - 我們將使用此套件將 JSON 簡維成 C# 類別屬性。

使用此工具從單一 JSON 文件中產生 C# 類別。 它需要一些微幅的變更，例如在 DB 資料表 (例如 urlData_port) 中將 JSON 陣列簡維成單一 C# 屬性，再簡維成 單一資料行 

* [JSON C# 類別產生器](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>代碼
您可以將此程式碼置於 `WorkerRole.cs`。

#### <a name="imports"></a>匯入
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>擷取儲存體連接字串
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>定期執行背景工作
取代現有的執行方法，並選擇您偏好的間隔。 因為匯出功能完成一個 JSON 物件需要一個小時，所以間隔時間至少須設定為一個小時。

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000

        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### <a name="insert-each-json-object-as-a-table-row"></a>插入每個 JSON 物件作為資料表資料列
    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
             {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
        //handle exception
      }
    }

#### <a name="parse-each-blob"></a>剖析每個 Blob
    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());

        string json;

        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());

          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));

          recCount = entities.Count();
          failureCount = 0; //resetting failure count

          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);

            Dictionary<string, object> dict = new Dictionary<string, object>();

            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");

            switch (FilterType)
            {
              case "PageViewPerformance":

              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
                }

              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }

            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);

            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;

            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### <a name="prepare-a-dictionary-for-each-json-document"></a>為每個 JSON 文件準備字典
    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }
        }

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>將 JSON 文件轉換成 C# 類別遙測物件屬性
     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }

            return res;
        }

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>從 JSON 文件中產生的 PageViewPerformance 類別檔案
    public class PageViewPerformance
    {
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>DBcontext for SQL interaction by Entity Framework
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

使用 `app.config` 中的 `TelemetryContext` 名稱新增資料庫連接字串。

## <a name="schema-information-only"></a>結構描述 (僅供參考)
這是將為 PageView 產生之資料表的結構描述。

> [!NOTE]
> 您不需要執行這個指令碼。 JSON 中的屬性決定了資料表中的資料行。
> 
> 

    CREATE TABLE [dbo].[PageViewPerformances](
    [Id] [uniqueidentifier] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlData_port] [int] NOT NULL,
    [urlData_protocol] [nvarchar](max) NULL,
    [urlData_host] [nvarchar](max) NULL,
    [urlData_base] [nvarchar](max) NULL,
    [urlData_hashTag] [nvarchar](max) NULL,
    [total_value] [float] NOT NULL,
    [networkConnection_value] [float] NOT NULL,
    [sendRequest_value] [float] NOT NULL,
    [receiveRequest_value] [float] NOT NULL,
    [clientProcess_value] [float] NOT NULL,
    [name] [nvarchar](max) NULL,
    [User] [nvarchar](max) NULL,
    [internal_data_id] [nvarchar](max) NULL,
    [internal_data_documentVersion] [nvarchar](max) NULL,
    [context_data_eventTime] [datetime] NULL,
    [context_device_id] [nvarchar](max) NULL,
    [context_device_type] [nvarchar](max) NULL,
    [context_device_os] [nvarchar](max) NULL,
    [context_device_osVersion] [nvarchar](max) NULL,
    [context_device_locale] [nvarchar](max) NULL,
    [context_device_userAgent] [nvarchar](max) NULL,
    [context_device_browser] [nvarchar](max) NULL,
    [context_device_browserVersion] [nvarchar](max) NULL,
    [context_device_screenResolution_value] [nvarchar](max) NULL,
    [context_user_anonId] [nvarchar](max) NULL,
    [context_user_anonAcquisitionDate] [nvarchar](max) NULL,
    [context_user_authAcquisitionDate] [nvarchar](max) NULL,
    [context_user_accountAcquisitionDate] [nvarchar](max) NULL,
    [context_session_id] [nvarchar](max) NULL,
    [context_session_isFirst] [bit] NOT NULL,
    [context_operation_id] [nvarchar](max) NULL,
    [context_location_point_lat] [float] NOT NULL,
    [context_location_point_lon] [float] NOT NULL,
    [context_location_clientip] [nvarchar](max) NULL,
    [context_location_continent] [nvarchar](max) NULL,
    [context_location_country] [nvarchar](max) NULL,
    [context_location_province] [nvarchar](max) NULL,
    [context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


若要查看此範例的實際執行狀況，請[下載](https://sesitai.codeplex.com/)完整的運作程式碼，變更 `app.config` 設定，然後將背景工作角色發佈到 Azure。

## <a name="next-steps"></a>後續步驟

* [撰寫程式碼來直接存取遙測](http://dev.applicationinsights.io/)
* [使用背景工作角色匯出至 SQL](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights 中的連續匯出](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [匯出資料模型](app-insights-export-data-model.md)
* [更多範例和逐步解說](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Jan17_HO4-->


