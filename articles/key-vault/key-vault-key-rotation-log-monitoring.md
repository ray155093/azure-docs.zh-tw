<properties
	pageTitle="如何使用端對端金鑰輪替和稽核設定金鑰保存庫 | Microsoft Azure"
	description="使用此操作說明可協助您使用金鑰輪替和監視金鑰保存庫記錄檔來進行設定"
	services="key-vault, automation,app-service\logic"
	documentationCenter=""
	authors="swgriffith"
	manager=""
	tags=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="jodehavi;stgriffi"/>
#如何使用端對端金鑰輪替和稽核設定金鑰保存庫

##簡介

在建立 Azure 金鑰保存庫之後，您可以開始運用該保存庫來儲存金鑰和密碼。應用程式不再需要保存金鑰或密碼，而是視需要從金鑰保存庫要求取得。這可讓您更新金鑰和密碼，而不會影響應用程式的行為，讓您有各種可能方式來管理金鑰和密碼。

本文逐步解說運用 Azure 金鑰保存庫來儲存密碼的範例，在此案例中就是應用程式所存取的 Azure 儲存體帳戶金鑰。文中也會示範如何實作該儲存體帳戶金鑰的排程輪替。最後，本文會逐步示範如何監視金鑰保存庫稽核記錄檔，並在提出未預期的要求時發出警示。

> [AZURE.NOTE] 本教學課程並非要詳細說明 Azure 金鑰保存庫的初始設定。如需這方面的資訊，請參閱[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。或者，如需跨平台命令列介面的指示，請參閱[這個對等的教學課程](key-vault-manage-with-cli.md)。

##設定金鑰保存庫

若要讓應用程式能夠從 Azure 金鑰保存庫擷取密碼，您必須先建立此密碼，並上傳至保存庫。透過 PowerShell 即可輕鬆完成這些工作，如下所示。

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。Azure PowerShell 會取得與此帳戶相關聯的所有訂用帳戶，並依預設使用第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立 Azure 金鑰保存庫的那一個特定訂用帳戶。輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzureRmSubscription
```

然後，輸入下列命令以指定與所要記錄之金鑰保存庫相關聯的訂用帳戶：

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

本文會示範如何將儲存體帳戶金鑰儲存為密碼，因此您必須取得該儲存體帳戶金鑰。

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

在擷取密碼之後，在此案例中就是儲存體帳戶金鑰，您必須將其轉換為安全字串，然後使用該值在金鑰保存庫中建立密碼。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
接下來您會想要取得您剛才建立之密碼的 URI。在稍後的步驟中，當您呼叫金鑰保存庫以擷取密碼時將會用到。執行下列 PowerShell 命令，並記下 'Id' 值，也就是密碼的 URI。

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##設定應用程式

現在您已擁有儲存的密碼，接下來您會想要擷取該密碼並透過程式碼來使用。需要執行幾個步驟才能達到這個目的，第一個也是最重要的步驟是向 Azure Active Directory 註冊應用程式，然後讓 Azure 金鑰保存庫知道應用程式的資訊，以便允許來自應用程式的要求。

> [AZURE.NOTE] 應用程式必須建立在與金鑰保存庫相同的 Azure Active Directory 租用戶上。

先開啟 Azure Active Directory 的 [應用程式] 索引標籤

![在 Azure AD 開啟應用程式](./media/keyvault-keyrotation/AzureAD_Header.png)

選擇 [新增] 將新的應用程式新增到 Azure AD

![選擇新增應用程式](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

讓應用程式類型保持使用 [Web 應用程式和/或 WEB API]，然後為應用程式提供名稱。

![為應用程式命名](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

為應用程式提供 [登入 URL] 和 [應用程式識別碼 URI]。這些可以是任何想用於此示範的值，並可在稍後視需要加以變更。

![提供必要的 URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

一旦應用程式新增至 Azure AD，您將會進入應用程式頁面。此後，按一下 [設定] 索引標籤，然後尋找並複製 [用戶端識別碼] 值。記下用戶端識別碼以供後續步驟使用。

接下來，您必須產生金鑰，以便應用程式能夠與 Azure AD 互動。您可以在 [設定] 索引標籤的 [金鑰] 區段底下建立此金鑰。記下從 Azure AD 應用程式新產生的金鑰，以供後續步驟使用。

![Azure AD 應用程式金鑰](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

在建立從應用程式到金鑰保存庫的任何呼叫之前，您必須讓金鑰保存庫知道應用程式及其權限。下列命令會從 Azure AD 應用程式取得保存庫名稱和用戶端識別碼，並授與 'Get' 權限給應用程式的金鑰保存庫。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

此時，您已準備好開始建置應用程式呼叫。在應用程式中，您必須先安裝所需的 NuGet 套件，以便與 Azure 金鑰保存庫和 Azure Active Directory 互動。從 Visual Studio Package Manager Console 輸入下列命令。請注意，本文撰寫當下的 ActiveDirectory 封裝最新版本是 3.10.305231913，因此請確認最新版本並據以更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在應用程式程式碼中，建立類別以保有 Active Directory 驗證的方法。在此範例中，該類別稱為 'Utils'。然後您必須新增下列 using。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接著，新增下列方法，以從 Azure AD 擷取 JWT 權杖。為了能夠維護，請將硬式編碼的字串值移動至 Web 或應用程式組態。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

最後，您可以加入必要的程式碼，以呼叫金鑰保存庫並擷取密碼值。首先，您必須加入下列 using 陳述式。

```csharp
using Microsoft.Azure.KeyVault;
```

接下來，您將新增方法呼叫來叫用金鑰保存庫，並擷取密碼。在這個方法中，您將提供您在先前的步驟中儲存的密碼 URI。請注意來自先前建立的 Utils 類別的 GetToken 方法的使用方式。
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

當您執行應用程式時，您現在應該向 Azure Active Directory 進行驗證，然後從 Azure 金鑰保存庫中擷取密碼值。

##使用 Azure 自動化的金鑰輪替

針對儲存為 Azure 金鑰保存庫密碼的值，有各種選項可用來實作其輪替策略。密碼可以在進行手動程序時輪替、利用 API 呼叫以程式設計的方式輪替，或是透過自動化指令碼來輪替。基於本文的目的，我們將會利用 Azure PowerShell 並結合 Azure 自動化，以變更 Azure 儲存體帳戶存取金鑰，然後我們將使用這個新的金鑰更新金鑰保存庫密碼。

為了允許 Azure 自動化在金鑰保存庫中設定密碼值，您必須取得建立 Azure 自動化執行個體時所建立、名為 'AzureRunAsConnection' 的連線的用戶端識別碼。您可以藉由從 Azure 自動化執行個體選擇 [資產]，來取得這個識別碼。在該處選擇 [連線]，然後選取 [AzureRunAsConnection] 服務主體。請記下 [應用程式識別碼]。

![Azure 自動化用戶端識別碼](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

於留在 [資產] 視窗時，也請選擇 [模組]。在模組中選取 [資源庫]，然後搜尋並 [匯入] 下列每個模組的更新版本。

	Azure
	Azure.Storage	
	AzureRM.Profile
	AzureRM.KeyVault
	AzureRM.Automation
	AzureRM.Storage
	
> [AZURE.NOTE] 本文撰寫當下，只有下面分享的指令碼才需要更新上述模組。如果您發現自動化作業失敗，請確認您已匯入所有必要的模組及其相依項目。

擷取 Azure 自動化連線的應用程式識別碼之後，您必須讓 Azure 金鑰保存庫知道，此應用程式有權更新保存庫中的密碼。這可以使用下列 PowerShell 命令來完成。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下來，選取 Azure 自動化執行個體底下的 [Runbook] 資源，並選取 [新增 Runbook]。選取 [快速建立]。為 Runbook 命名，然後選取 [PowerShell] 做為 Runbook 類型。選擇性地新增描述。最後，按一下 [建立]。

![建立 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

在新的 Runbook 的編輯器窗格中，貼上下列 PowerShell 指令碼。

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在編輯器窗格中，您可以選擇 [測試窗格] 來測試指令碼。一旦指令碼在執行時不會發生錯誤，您可以選取 [發佈] 選項，然後回到 Runbook 的組態窗格套用 Runbook 的排程。

##金鑰保存庫稽核管線

當您設定 Azure 金鑰保存庫時，您可以開啟稽核功能，以收集對金鑰保存庫提出的存取要求的記錄檔。這些記錄檔會儲存在指定的 Azure 儲存體帳戶，然後可供提取、監視和分析。以下會逐步說明如何運用 Azure Functions、Azure Logic Apps 和金鑰保存庫稽核記錄檔來建立管線，以在與 Web 應用程式的應用程式識別碼不符的應用程式擷取保存庫中的密碼時傳送電子郵件的案例。

首先，您必須在金鑰保存庫上啟用記錄功能。這可以透過下列 PowerShell 命令來完成 ([這裡](key-vault-logging.md)可以看到完整詳細資料)：

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

一旦啟用此功能，就會開始將稽核記錄檔收集到指定的儲存體帳戶。這些記錄檔會包含金鑰保存庫存取方式、時間和存取者的事件。

> [AZURE.NOTE] 您最多在執行過金鑰保存庫作業 10 分鐘後，就能存取其記錄資訊。但大多不用這麼久。

下一步是[建立 Azure 服務匯流排佇列](../service-bus/service-bus-dotnet-get-started-with-queues.md)。這是金鑰保存庫稽核記錄檔的推送位置。一旦進入佇列，邏輯應用程式就會加以提取並採取行動。建立服務匯流排相當簡單，以下是高階步驟︰

1. 建立服務匯流排命名空間 (如果您已經有一個想要用於此作業的命名空間，請跳至步驟 2)。
2. 在入口網站中瀏覽至服務匯流排，然後選取要在其中建立佇列的命名空間。
3. 選取 [新增]，然後選擇 [服務匯流排] -> [佇列]，並輸入必要詳細資料。
4. 選擇命名空間，然後按一下 [連接資訊]，以取得服務匯流排連接資訊。下一個部分需要此資訊。

接下來，您將[建立 Azure 函式](../azure-functions/functions-create-first-azure-function.md)以輪詢儲存體帳戶中的金鑰保存庫記錄，並挑選新的事件。這是會依排程觸發的函式。

建立 Azure 函式 (在入口網站中選擇 [新增] -> [函式應用程式])。在建立期間，您可以使用現有的主控方案，或建立新的方案。您也可以選擇動態主控。[這裡](../azure-functions/functions-scale.md)可以找到關於函式主控選項的詳細資訊。

在建立 Azure 函式時，請瀏覽到該函式，選擇計時器函式和 C#，然後按一下 [開始] 畫面中的 [建立]。

![Azure Functions 啟動刀鋒視窗](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在 [開發] 索引標籤上，以下列內容取代 run.csx 程式碼︰

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> [AZURE.NOTE] 請務必取代上述程式碼中的變數，以指向金鑰保存庫記錄所寫入到的儲存體帳戶、稍早建立的服務匯流排，以及金鑰保存庫儲存體記錄的特定路徑。

此函式會挑選金鑰保存庫記錄所寫入到的儲存體帳戶中最新的記錄檔、取得來自該檔案的最新事件，並推送到服務匯流排佇列。由於單一檔案可以有多個事件 (例如一整個小時的事件)，於是我們建立函式也會查看的「sync.txt」檔案，以判斷所挑選的最後一個事件的時間戳記。這可確保我們不會推送相同事件多次。這個「sync.txt」檔案只包含最後發生之事件的時間戳記。記錄檔在載入時，必須根據時間戳記加以排序，以確保記錄檔會以正確順序排序。

在這個函式中，我們會參考 Azure Functions 中數個必非立即可用的額外程式庫。若要加入這些程式庫，我們需要 Azure Functions 來使用 nuget 提取它們。選擇 [檢視檔案] 選項

![檢視檔案選項](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

並使用下列內容新增稱為「project.json」的新檔案︰

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
在「儲存」時，這會觸發 Azure Functions 下載所需的二進位檔。

切換至 [整合] 索引標籤，然後為計時器參數提供有意義的名稱以在函式內使用。上面的程式碼預期計時器名稱為「myTimer」。如下所示指定 [CRON 運算式](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON)︰0 * * * * * 以用於計時器，而這會導致函式每分鐘執行一次。

在同一個 [整合] 索引標籤中，新增類型為 [Azure Blob 儲存體] 的輸入。這會指向包含函式所查看之最後一個事件的時間戳記的「sync.txt」檔案。這會在函式中依參數名稱來提供。在上面的程式碼中，Azure Blob 儲存體輸入預期參數名稱為「inputBlob」。選擇「sync.txt」檔案所位於的儲存體帳戶 (可能是相同或不同的儲存體帳戶)，然後在 [路徑] 欄位以 {container-name}/path/to/sync.txt 格式提供檔案所在位置的路徑。

新增類型為 [Azure Blob 儲存體] 輸出的輸出。這也會指向您剛才在輸入中定義的「sync.txt」檔案。這可供函式用來寫入所查看的最後一個事件的時間戳記。上面的程式碼預期此參數名稱為「outputBlob」。

到此時，函式已準備就緒。請務必切換回 [開發] 索引標籤並「儲存」程式碼。檢查 [輸出] 視窗中是否有任何編譯錯誤，並據以修正。如果它進行編譯，此程式碼現在應該正在執行，然後每隔一分鐘會檢查金鑰保存庫的記錄檔，並將任何新事件推送到已定義的服務匯流排佇列。您應該會在每次觸發函式時看到記錄資訊寫入到 [記錄檔] 視窗。

###Azure 邏輯應用程式

接下來，我們必須建立 Azure 邏輯應用程式，以挑選函式推送至服務匯流排佇列的事件、剖析內容，以及根據符合的條件傳送電子郵件。

移至 [新增] -> [邏輯應用程式] 以[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

一旦建立了邏輯應用程式，請瀏覽到該應用程式，然後選擇 [編輯]。在邏輯應用程式編輯器中，選擇「服務匯流排佇列」管理的 API，並輸入服務匯流排認證以將其連接至佇列。

![Azure 邏輯應用程式服務匯流排](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

接下來選擇 [新增條件]。在條件中，切換到「進階編輯器」並輸入下列內容，然後將 APP\_ID 取代為實際的 Web 應用程式 APP\_ID：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

如果來自內送事件的 **appid** 屬性 (這是服務匯流排訊息的內文) 不是應用程式的 **appid**，這個運算式基本上會傳回 **false**。

現在，在 [若為否則不執行任何動作...] 選項底下建立動作。

![Azure 邏輯應用程式選擇動作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

針對動作選擇 [Office 365 - 傳送電子郵件]。填寫欄位，以建立定義的條件傳回 false 時要傳送的電子郵件。如果您沒有 Office 365，您可以查看能夠達到相同效果的替代方案。

此時，您已擁有端對端管線，它會每分鐘一次尋找是否有新的金鑰保存庫稽核記錄檔。每當它找到新的記錄檔，就會將其傳送至服務匯流排佇列。一旦有新的訊息進入佇列，就會觸發邏輯應用程式，如果事件內的 appid 不符合呼叫端應用程式的應用程式識別碼，則會傳送電子郵件。

<!---HONumber=AcomDC_0713_2016-->