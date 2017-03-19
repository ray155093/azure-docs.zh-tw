---
title: "使用 C# 來部署 Azure 虛擬機器 | Microsoft Docs"
description: "使用 C# 和 Azure Resource Manager 來部署 Azure 虛擬機器及所有支援它的資源。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>使用 C 來部署 Azure 虛擬機器# #

本文說明如何使用 C# 來建立 Azure 虛擬機器及支援它的資源。

執行這些步驟大約需要 20 分鐘的時間。

## <a name="step-1-create-a-visual-studio-project"></a>步驟 1：建立 Visual Studio 專案

在此步驟中，您會確定是否已安裝 Visual Studio，並建立用來建立資源的主控台應用程式。

1. 如果您尚未安裝 [Visual Studio](https://www.visualstudio.com/)，請進行安裝。
2. 在 Visual Studio 中，按一下 [檔案] > [新增] > [專案]。
3. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。

## <a name="step-2-install-libraries"></a>步驟 2：安裝程式庫

NuGet 套件是安裝完成這些步驟所需之程式庫的最簡單方式。 若要取得在 Visual Studio 中所需要的程式庫，請執行下列步驟：


1. 在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵、按一下 [管理 NuGet 套件]，然後按一下 [瀏覽]。
2. 在搜尋方塊中輸入 *Microsoft.IdentityModel.Clients.ActiveDirectory*，按一下 [安裝]，然後依照指示操作來安裝套件。
3. 在頁面的頂端，選取 [包含發行前版本] 。 在搜尋方塊中輸入 *Microsoft.Azure.Management.Compute*，按一下 [安裝]，然後依照指示操作來安裝套件。
4. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Network*，按一下 [安裝]，然後依照指示操作來安裝套件。
5. 在搜尋方塊中輸入 *Microsoft.Azure.Management.Storage*，按一下 [安裝]，然後依照指示操作來安裝套件。
6. 在搜尋方塊中輸入 *Microsoft.Azure.Management.ResourceManager*，按一下 [安裝]，然後依照指示操作來安裝套件。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>步驟 3：建立用來驗證要求的認證

在您開始此步驟之前，請確定您可以存取 [Active Directory 服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)。 從服務主體中，您會取得向 Azure Resource Manager 驗證要求的權杖。

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：
   
    ```
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
    ```

2. 若要取得建立認證所需的權杖，請將下列方法新增至 Program 類別：
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    取代下列值：
    
    - 將 *{client-id}* 取代成 Azure Active Directory 應用程式的識別碼。 您可以在 AD 應用程式的 [屬性] 刀鋒視窗上找到此識別碼。 若要在 Azure 入口網站中尋找您的 AD 應用程式，請按一下資源功能表中的 [Azure Active Directory]，然後按一下 [應用程式註冊]。
    - 將 *{client-secret}* 取代成 AD 應用程式的存取金鑰。 您可以在 AD 應用程式的 [屬性] 刀鋒視窗上找到此識別碼。
    - 將 *{tenant-id}* 取代成您訂用帳戶的租用戶識別碼。 您可以在 Azure 入口網站中 Azure Active Directory 的 [屬性] 刀鋒視窗上找到租用戶識別碼。 其標示為 [目錄識別碼]。

3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Program.cs 檔案中的 Main 方法：
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. 儲存 Program.cs 檔案。

## <a name="step-3-create-the-resources"></a>步驟 3：建立資源

### <a name="register-the-providers-and-create-a-resource-group"></a>註冊提供者，並建立資源群組

所有資源都必須包含在資源群組中。 您的訂用帳戶必須先向資源提供者註冊，您才能將資源新增至群組。

1. 將變數加入至 Program 類別的 Main 方法，以指定您要使用的資源名稱：

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    取代下列值：

    - 將開頭為 *my* 的所有資源名稱取代成您環境適用的名稱。
    - 將 *subscriptionId* 取代成您的訂用帳戶 ID。 您可以在 Azure 入口網站的 [訂用帳戶] 刀鋒視窗上找到訂用帳戶 ID。
    - 將 *location* 取代成您要建立資源的 [Azure 區域](https://azure.microsoft.com/regions/)。
    - 將 *adminName* 取代成虛擬機器上系統管理帳戶的名稱。
    - 將 *adminPassword* 取代成系統管理員帳戶的密碼。

2. 若要建立資源群組並註冊提供者，請將下列方法新增至 Program 類別：
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      Console.WriteLine("Registering the providers...");
      var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
      Console.WriteLine(rpResult.RegistrationState);
   
      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>建立儲存體帳戶

使用受管理的磁碟時，需要有一個[儲存體帳戶](../storage/storage-create-storage-account.md)來儲存為虛擬機器建立的虛擬硬碟檔案。

1. 若要建立儲存體帳戶，請將下列方法新增至 Program 類別：

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

必須要有公用 IP 位址才能與虛擬機器進行通訊。

1. 若要為虛擬機器建立公用 IP 位址，請將下列方法新增至 Program 類別：
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用「資源管理員」部署模型建立的虛擬機器必須在虛擬網路中。

1. 若要建立子網路和虛擬網路，請將下列方法新增至 Program 類別：

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>建立網路介面

虛擬機器需要網路介面以在虛擬網路上通訊。

1. 若要建立網路介面，請將下列方法新增至 Program 類別：

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>建立可用性設定組

管理您應用程式所使用之虛擬機器的維護時，使用可用性設定組可讓您更加輕鬆。

1. 若要建立可用性設定組，請將下列方法新增至 Program 類別：

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>建立虛擬機器

既然您已經建立所有支援的資源，您可以建立虛擬機器。

1. 若要建立虛擬機器，請將下列方法新增至 Program 類別：

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    }
    ```

    > [!NOTE]
    > 本教學課程中會建立執行 Windows Server 作業系統版本的虛擬機器。 若要深入了解如何選取其他映像，請參閱 [使用 Windows PowerShell 和 Azure CLI 來瀏覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
    > 
    >

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      nicName,
      avSetName,
      storageName,
      adminName,
      adminPassword,
      vmName);
    Console.WriteLine(vmResult.Result.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-4-delete-the-resources"></a>步驟 4：刪除資源

由於您需要為在 Azure 中使用的資源付費，因此刪除不再需要的資源一律是理想的做法。 如果您想要刪除虛擬機器及所有支援的資源，您只需要刪除資源群組。

1. 若要刪除資源群組，請將下列方法新增至 Program 類別：
   
    ```
    public static async void DeleteResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId)
    {
      Console.WriteLine("Deleting resource group...");
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>步驟 5：執行主控台應用程式

1. 若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

2. 在出現 [成功] 狀態之後，按 **Enter**。 
   
3. 在建立虛擬機器之後且在按 **Enter** 以開始刪除資源之前，您可以先花幾分鐘在 Azure 入口網站中檢查資源。

## <a name="next-steps"></a>後續步驟
* 使用 [利用 C# 和 Resource Manager 範本來部署 Azure 虛擬機器](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中的資訊，以利用範本來建立虛擬機器。
* 檢閱[使用 Azure Resource Manager 和 C# 管理 Azure 虛擬機器#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以了解如何管理您建立的虛擬機器。


