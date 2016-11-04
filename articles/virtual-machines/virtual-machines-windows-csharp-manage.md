---
title: 使用 Azure Resource Manager 和 C# 管理 VM | Microsoft Docs
description: 使用 Azure Resource Manager 和 C# 管理虛擬機器。
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/24/2016
ms.author: davidmu

---
# 使用 Azure Resource Manager 和 C# 管理 Azure 虛擬機器
本文章中的工作會示範如何管理虛擬機器，例如啟動、停止及更新。

若要完成本文中的工作，您需要：

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [驗證權杖](../resource-group-authenticate-service-principal.md)

## 建立 Visual Studio 專案並安裝封裝
NuGet 封裝是安裝完成本文中工作所需程式庫最簡單的方式。您必須安裝 Azure Active Directory Authentication Library 和 Computer Resource Provider Library。若要在 Visual Studio 中取得這些程式庫，請執行下列動作：

1. 按一下 [檔案] > [新增] > [專案]。
2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。
3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]。
4. 在搜尋方塊中輸入 *Active Directory*，對 Active Directory Authentication Library 封裝按一下 [**安裝**]，然後依照指示，安裝封裝。
5. 在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 *Microsoft.Azure.Management.Compute*，對 Compute .NET Library 按一下 [安裝]，然後遵循指示來安裝封裝。

現在，您已經準備就緒，可以開始使用程式庫來管理虛擬機器。

## 設定專案
既然已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您要將應用程式資訊格式化成用來驗證對 Azure 資源管理員的要求的認證。

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. 將變數新增至 Program 類別的 Main 方法，以指定您要管理之資源的名稱、資源的位置 (例如 "Central US") 以及您的訂用帳戶識別碼：
   
        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";
   
    以您想要使用的名稱和識別碼取代所有變數值。您可以執行 Get-AzureRmSubscription 來尋找訂用帳戶識別碼。
3. 將下列方法新增至 Program 類別，以取得建立認證所需的權杖。
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return result;
        }
   
    將 {client-id} 用 Azure Active Directory 應用程式的識別碼取代，將 {client-secret} 用 AD 應用程式的存取金鑰取代，並將 {tenant-id} 用您訂用帳戶的租用戶識別碼取代。您可以透過執行 Get-AzureRmSubscription 來尋找租用戶識別碼。您可以使用 Azure 入口網站尋找存取金鑰。
4. 將下列程式碼新增至 Program.cs 的 Main 方法，以建立認證：
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
5. 儲存 Program.cs 檔案。

## 顯示虛擬機器的相關資訊
1. 將此方法新增至您先前所建立專案的 Program 類別：
   
        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");
   
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);
   
          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);
   
          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
   
          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
   
          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }
   
          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }
   
          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }
   
          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
   
        }
2. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
3. 儲存 Program.cs 檔案。
4. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    當您執行此方法時，應該會看到如下的結果：
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## 啟動虛擬機器
1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }
3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的狀態變更為 [執行中]。

## 停止虛擬機器
您可以使用兩種方式停止虛擬機器。您可以停止虛擬機器並保留其所有設定，但仍繼續計費，也可以停止虛擬機器並將其解除配置，這樣也會解除配置其相關聯的所有資源，並結束虛擬機器的計費。

1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }
   
    如果您想要解除配置虛擬機器，請將 PowerOff 呼叫變更為下列項目︰
   
        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的狀態變更為 [已停止]。如果您執行的方法呼叫「解除配置」，則狀態為 [已停止 (已解除配置)]。

## 重新啟動執行中虛擬機器
1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }
3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

## 刪除虛擬機器
1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }
3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

## 更新虛擬機器
此範例示範如何變更執行中虛擬機器的大小。

1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }
3. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：
   
        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的大小變更為 Standard\_A1。

## 後續步驟
如果部署發生問題，請查看[使用 Azure 入口網站來檢視部署作業](../resource-manager-troubleshoot-deployments-portal.md)

<!---HONumber=AcomDC_0720_2016-->