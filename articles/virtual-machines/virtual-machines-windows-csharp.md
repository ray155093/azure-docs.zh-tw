<properties
	pageTitle="使用 C# 部署 Azure 資源 | Microsoft Azure"
	description="了解如何使用 C# 和 Azure Resource Manager 建立 Microsoft Azure 資源。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# 使用 C 部署 Azure 資源# 

本文說明如何使用 C# 建立 Azure 資源。

請確定您已完成下列項目：

- 安裝 [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- 驗證 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) 的安裝
- 取得[驗證權杖](../resource-group-authenticate-service-principal.md)

執行這些步驟需要 30 分鐘左右。

## 步驟 1：建立 Visual Studio 專案，並安裝程式庫

NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。您必須安裝 Azure Resource Management Library、Azure Active Directory Authentication Library，以及 Computer Resource Provider Library。若要在 Visual Studio 中取得這些程式庫，請執行下列動作：

1. 按一下 [檔案] > [新增] > [專案]。

2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]。

4. 在搜尋方塊中輸入 *Active Directory*，對 Active Directory Authentication Library 封裝按一下 [**安裝**]，然後依照指示，安裝封裝。

5. 在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 *Microsoft.Azure.Management.Compute*，對 Compute .NET Library 按一下 [安裝]，然後遵循指示來安裝封裝。

6. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Network*，對 Network .NET Library 按一下 [安裝]，然後遵循指示來安裝封裝。

7. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Storage*，對 Storage .NET Library 按一下 [安裝]，然後遵循指示來安裝封裝。

8. 在搜尋方塊中輸入 *Microsoft.Azure.Management.ResourceManager*，對 Resource Management Library 按一下 [安裝]。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

## 步驟 2：建立用來驗證要求的認證

已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您現在要將應用程式資訊格式化成用來驗證對 Azure Resource Manager 的要求的認證。執行此動作：

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. 將下列方法新增至 Program 類別，以取得建立認證所需的權杖：

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

	將 {client-id} 用 Azure Active Directory 應用程式的識別碼取代，將 {client-secret} 用 AD 應用程式的存取金鑰取代，並將 {tenant-id} 用您訂用帳戶的租用戶識別碼取代。您可以透過執行 Get-AzureRmSubscription 來尋找租用戶識別碼。您可以使用 Azure 入口網站尋找存取金鑰。

3. 將下列程式碼新增至 Program.cs 檔案的 Main 方法以建立認證：

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. 儲存 Program.cs 檔案。

## 步驟 3：加入程式碼以註冊提供者，並建立資源

### 註冊提供者，並建立資源群組

所有資源都必須包含在資源群組中。您的訂用帳戶必須先向資源提供者註冊，您才能將資源新增至群組。

1. 將變數新增至 Program 類別的 Main 方法，以指定您要用於資源的名稱、資源的位置 (例如 "Central US")、系統管理員帳戶資訊，以及您的訂用帳戶識別碼：

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    以您想要使用的名稱和識別碼取代所有變數值。您可以執行 Get-AzureRmSubscription 來尋找訂用帳戶識別碼。

2. 將下列方法新增至 Program 類別以建立資源群組並註冊提供者：

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### 建立儲存體帳戶

儲存針對虛擬機器建立的虛擬硬碟檔案時，需要[儲存體帳戶](../storage/storage-create-storage-account.md)。

1. 將下列方法新增至 Program 類別以建立儲存體帳戶：

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### 建立公用 IP 位址

必須要有公用 IP 位址才能與虛擬機器進行通訊。

1. 將下列方法新增至 Program 類別以建立虛擬機器的公用 IP 位址：

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### 建立虛擬網路

使用「資源管理員」部署模型建立的虛擬機器必須在虛擬網路中。

1. 將下列方法新增至 Program 類別，以建立子網路和虛擬網路：

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### 建立網路介面

虛擬機器需要網路介面來在剛才建立的虛擬網路上通訊。

1. 將下列方法新增至 Program 類別以建立網路介面：

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### 建立可用性設定組

管理您應用程式所使用之虛擬機器的維護時，使用可用性設定組可讓您更加輕鬆。

1. 將下列方法新增至 Program 類別以建立可用性設定組：

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. 將下列程式碼新增至 Program 類別的 Main 方法，以呼叫您剛才新增的方法：

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### 建立虛擬機器

既然您已經建立所有支援的資源，您可以建立虛擬機器。

1. 將下列方法新增至 Program 類別以建立虛擬機器：

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential);
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
          Console.WriteLine(vm.ProvisioningState);
        }

	>[AZURE.NOTE] 本教學課程中會建立執行 Windows Server 作業系統版本的虛擬機器。若要深入了解如何選取其他映像，請參閱[使用 Windows PowerShell 和 Azure CLI 巡覽並選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

2. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avsetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##步驟 4：加入程式碼以刪除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。如果您想要刪除虛擬機器及所有支援的資源，您只需要刪除資源群組。

1.	將下列方法新增至 Program 類別以刪除資源群組：

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## 步驟 5：執行主控台應用程式

1. 若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

2. 傳回每個狀態碼之後，按下 **Enter** 以建立每個資源。建立虛擬機器之後，請執行下一個步驟，再按 Enter 以刪除所有資源。

	此主控台應用程式從開始到完成的完整執行應該需要 5 分鐘左右。在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure 入口網站中建立的資源，然後再刪除。

3. 瀏覽至 Azure 入口網站中的稽核記錄，以查看資源的狀態：

	![在 Azure 入口網站中瀏覽稽核記錄檔](./media/virtual-machines-windows-csharp/crpportal.png)
    
## 後續步驟

- 使用[利用 C# 和 Resource Manager 範本來部署 Azure 虛擬機器](virtual-machines-windows-csharp-template.md)中的資訊，以利用範本來建立虛擬機器。
- 請參閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-csharp-manage.md)，了解如何管理您剛才建立的虛擬機器。

<!---HONumber=AcomDC_0720_2016-->