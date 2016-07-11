<properties
   pageTitle="Resource Manager SDK for .NET| Microsoft Azure"
   description="Resource Manager .NET SDK 驗證和使用方式範例的概觀"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK for .NET  
Azure Resource Manager SDK 可供多個語言和平台使用。這些語言實作都是透過其生態系統的封裝管理員和 GitHub 提供。

每個 SDK 中的程式碼都是從 [Azure RESTful API 規格](https://github.com/azure/azure-rest-api-specs)產生。這些規格是開放原始碼，以 Swagger 2.0 規格為基礎。SDK 程式碼是透過稱為 [AutoRest](https://github.com/azure/autorest) 的開放原始碼專案所產生。AutoRest 會將這些 RESTful API 規格轉換成多種語言版本的用戶端程式庫。如果您想要改善 SDK 中所產生之程式碼的任何層面，用來建立 SDK 的完整工具集皆開放免費取得，且是根據廣為採用的 API 規格格式。

[Azure SDK for .NET](https://azure.microsoft.com/downloads/) 是一組 NuGet 封裝，可協助您呼叫大多數的 Azure Resource Manager API。如果 SDK 未提供必要的功能，您可以在幕後輕鬆地結合 SDK 與對 Resource Manager REST API 的一般呼叫。

本為的目的並非說明 Azure SDK for .NET、Azure Resource Manager API 或 Visual Studio 的所有層面，而是要做為供您快速開始使用的方法。

下列所有程式碼片段皆來自[可下載的範例專案](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)。

## 安裝 NuGet 套件

本文中的範例需要兩個 NuGet 封裝 (除了 Azure SDK for .NET 以外)。若要進行安裝：

1. 在 Visual Studio 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]。
2. 搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory** 並安裝最新穩定版本的封裝。
3. 搜尋 **Microsoft.Azure.Management.ResourceManager**，然後選取 [包含發行前版本]。安裝最新的預覽版本 (例如 1.1.2-preview)。

## 驗證
Azure Active Directory (Azure AD) 會處理 Resource Manager 的驗證。若要連線到任何 API，您必須先向 Azure AD 進行驗證，以接收可以傳遞給每個要求的存取權杖。若要取得此權杖，您必須建立將用來進行登入的 Azure AD 應用程式和服務主體。如需逐步指示，請遵循下列其中一篇文章︰

- [使用 Azure PowerShell 來建立可存取資源的 Active Directory 應用程式](resource-group-authenticate-service-principal.md)
- [使用 Azure CLI 來建立可存取資源的 Active Directory 應用程式](resource-group-authenticate-service-principal-cli.md)
- [使用 Azure 入口網站來建立可存取資源的 Active Directory 應用程式](resource-group-create-service-principal-portal.md)

建立服務主體之後，您應該有︰

- 用戶端識別碼或應用程式識別碼 (GUID)
- 用戶端密碼 (字串)
- 租用戶識別碼 (GUID) 或網域名稱 (字串)

### 透過程式碼接收存取權杖
您可以透過以下幾行程式碼，只傳入您的 Azure AD 租用戶識別碼、您的 Azure AD 應用程式用戶端識別碼和 Azure AD 應用程式用戶端密碼，藉此來取得存取權杖。儲存此權杖即可用於數個要求，因為權杖的有效時間預設為 1 小時。

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

除了使用租用戶識別碼進行登入，您也可以如以下程式碼所示使用 Azure AD 網域。使用此方法會要求您變更方法簽章，以納入網域名稱，而不是租用戶識別碼。

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

您可以透過以下程式碼，為使用憑證進行驗證的 Azure AD 應用程式取得存取權杖︰

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### 查詢附加至已驗證應用程式的 Azure 訂用帳戶
您可能會想要查詢與剛才驗證的應用程式相關聯的 Azure 訂用帳戶。您鎖定的訂用帳戶的訂用帳戶識別碼必須傳遞至您從現在起進行的每個 API 呼叫。

下列範例程式碼會使用 REST API 來直接查詢 Azure API。也就是說，它不使用 Azure SDK for .NET 的任何功能。

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

請注意，您會從 Azure 收到 JSON 回應。然後，您可以從這個回應擷取出訂用帳戶識別碼，以傳回識別碼清單。本文的所有後續 Azure Resource Manager API 呼叫都會使用單一的 Azure 訂用帳戶識別碼。因此，如果您的應用程式與數個訂用帳戶相關聯，請只挑選正確的訂用帳戶，並將它傳遞做為接下來的參數。

從這裡開始，您對 Azure API 所進行的每個呼叫都會使用 Azure SDK for .NET。因此程式碼看起來會有點不同。

### 將權杖包裝為 TokenCredentials 物件
下列所有 API 呼叫都需要您從 Azure AD 接收的權杖 (其格式為 TokenCredentials object)。只要將原始權杖當作參數傳遞至類別的建構函式，即可輕鬆建立此種物件。

```csharp
var credentials = new TokenCredentials(token);
```

如果您有舊版的 Manager NuGet 封裝 (名為 Microsoft.Azure.Management.Resources)，則必須使用下列程式碼︰

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## 建立資源群組
Azure 中的所有一切皆以資源群組為重心，因此讓我們先建立一個資源群組。「ResourceManagementClient」可處理一般的資源和資源群組。對於您將使用的下列任何一個更特殊的管理用戶端，您必須提供您的認證和訂用帳戶識別碼，以識別您想要使用的訂用帳戶。

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## 透過手動方式或範本建立資源
有數種方式可與 Azure Resource Manager API 互動，但主要方式有二︰

* 手動方式，透過呼叫特定資源提供者來進行
* 透過使用 Azure Resource Manager 範本

使用 Resource Manager 範本具有下列優點︰

* 以宣告方式指定您希望結果呈現的樣貌，而非指定應如何達成。
* 您不必手動處理您的部署的平行執行。Resource Manager 會為您進行。
* 您不必為了部署 Resource Manager 範本而學習 C# 或任何其他語言，即使您可以使用任何語言來開始樣板化部署。
* 範本中使用的網域指定的語言 (DSL) 是透過 JSON 來建置。只要是使用過 JSON 的人，應該都會發現它很容易理解。

即使範本具有上述各項優點，一開始我們還是要先向您介紹如何以手動方式呼叫 API。

### 逐步建立虛擬機器
您已擁有訂用帳戶和資源群組。如果您想要部署虛擬機器 (VM)，就必須知道其組成︰

* 一或多個儲存體帳戶，用來儲存永續性磁碟
* 一或多個公用 IP 位址，讓您在 Azure 中的資源可供從網際網路 (包括 DNS 名稱) 進行存取
* 一或多個虛擬網路，讓您的資源可彼此進行內部通訊
* 一或多個網路介面卡 (NIC)，以允許 VM 進行通訊
* 一或多部虛擬機器，以執行軟體

在這些資源之中，有些可以平行建立，有些則不行。例如：

* NIC 相依於公用 IP 位址與虛擬網路。
* VM 相依於 NIC 和儲存體帳戶。

在建立好必要相依性之前，請勿嘗試具現化任何資源。完整[範例](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)會示範如何有效地以平行方式建立資源，同時仍繼續追蹤已建立的項目。

#### 建立儲存體帳戶
您需要有儲存體帳戶，才能儲存虛擬機器的虛擬硬碟。如果您已有儲存體帳戶，則可將它用於數個 VM。但請記得將負載分散到數個儲存體帳戶，以免遭遇到限制。也請記住，儲存體帳戶的類型及其位置可能會限制您可以選擇的 VM 大小，因為並非所有 VM 大小都適用於所有區域或所有儲存體帳戶類型。

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### 建立公用 IP 位址
公用 IP 位址讓您在 Azure 中的資源可從網際網路進行存取。除了 IP 位址，也會指派完整網域名稱 (FQDN) 給您，讓您更容易存取。

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### 建立虛擬網路
透過 Resource Manager API 建立的每部 VM 都必須屬於虛擬網路，即使 VM 單獨位於其中。虛擬網路必須包含至少一個子網路，但您可以使用多個子網路來分割並保護您的資源。

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### 建立網路介面卡
NIC 可連接您的 VM 與其所在的虛擬網路。一部 VM 可以有多個 NIC，因此會與數個虛擬網路相關聯。此範例假設您要將您的 VM 連結到僅僅一個虛擬網路。

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### 建立虛擬機器
最後，我們可以開始建立虛擬機器了。VM (直接或間接) 相依於先前建立的所有資源，因此您必須等到所有資源都已就緒，再嘗試佈建 VM。佈建 VM 所需的時間比建立其他資源更久，所以您的應用程式應該會等候一段時間，才能完成此作業。

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### 部署範本
請閱讀[使用 .NET 程式庫和範本部署 Azure 資源](./virtual-machines/virtual-machines-windows-csharp-template.md)一文，以取得如何部署範本的詳細指示。

簡單地說，部署範本比手動佈建資源更加輕鬆。下列程式碼會指向您的範本和參數檔所在的 URI，為您示範如何部署範本。

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```

<!---HONumber=AcomDC_0629_2016-->