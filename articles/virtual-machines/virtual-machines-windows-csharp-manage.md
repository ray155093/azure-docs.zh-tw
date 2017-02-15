---
title: "使用 Azure Resource Manager 和 C# 管理 VM | Microsoft Docs"
description: "使用 Azure Resource Manager 和 C# 管理虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d0c334a0b1e047208f6f61e5181864b1c2e3a6a


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>使用 Azure Resource Manager 和 C# 管理 Azure 虛擬機器
本文章中的工作會示範如何管理虛擬機器，例如啟動、停止及更新。 虛擬機器必須存在於資源群組中，才能完成這篇文章中的工作。

若要完成本文中的工作，您需要：

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [驗證權杖](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>建立 Visual Studio 專案並安裝封裝
NuGet 封裝是安裝完成本文中工作所需程式庫最簡單的方式。 您針對本文安裝的程式庫是 Azure Active Directory Authentication Library 以及 Compute Resource Provider Library。 請執行下列步驟，以在 Visual Studio 中取得程式庫：

1. 按一下 [檔案] > [新增] > [專案]。
2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。
3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝] 。
4. 在搜尋方塊中輸入 *Active Directory* ，對 Active Directory Authentication Library 封裝按一下 [ **安裝** ]，然後依照指示，安裝封裝。
5. 在頁面的頂端，選取 [包含發行前版本] 。 在搜尋方塊中輸入 *Microsoft.Azure.Management.Compute*，對 Compute .NET Library 按一下 [安裝]，然後遵循指示來安裝封裝。

現在，您已經準備就緒，可以開始使用程式庫來管理虛擬機器。

## <a name="set-up-the-project"></a>設定專案
現在，已建立應用程式並安裝程式庫，您會使用應用程式資訊建立權杖。 此權杖會用來驗證對 Azure Resource Manager 的要求。

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. 將變數新增至 Program 類別的 Main 方法，以指定資源群組的名稱、虛擬機器名稱，以及您的訂用帳戶識別碼：
   
        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";
   
    您可以執行 Get-AzureRmSubscription 來尋找訂用帳戶識別碼。
3. 若要取得建立認證所需的權杖，將下列方法新增至 Program 類別：
   
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
   
    將 {client-id} 用 Azure Active Directory 應用程式的識別碼取代，將 {client-secret} 用 AD 應用程式的存取金鑰取代，並將 {tenant-id} 用您訂用帳戶的租用戶識別碼取代。 您可以透過執行 Get-AzureRmSubscription 來尋找租用戶識別碼。 您可以使用 Azure 入口網站尋找存取金鑰。
4. 若要建立認證，將下列程式碼新增至 Program.cs 的 Main 方法：
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
5. 儲存 Program.cs 檔案。

## <a name="display-information-about-a-virtual-machine"></a>顯示虛擬機器的相關資訊
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
2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
3. 儲存 Program.cs 檔案。
4. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    當您執行此方法時，應該會看到如下的範例：
   
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

## <a name="stop-a-virtual-machine"></a>停止虛擬機器
您可以使用兩種方式停止虛擬機器。 您可以停止虛擬機器並保留其所有的設定，但仍繼續計費，或您可以停止虛擬機器並將其解除配置。 當解除配置虛擬機器時，與其相關聯的所有資源也都會解除配置且其計費會結束。

1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void StopVirtualMachineAsync(
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
   
    如果您想要解除配置虛擬機器，請將 PowerOff 呼叫變更為下列程式碼︰
   
        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的狀態變更為 [已停止]。 如果您執行的方法呼叫「解除配置」，則狀態為 [已停止 (已解除配置)]。

## <a name="start-a-virtual-machine"></a>啟動虛擬機器
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
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的狀態變更為 [執行中]。

## <a name="restart-a-running-virtual-machine"></a>重新啟動執行中虛擬機器
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
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

## <a name="resize-a-virtual-machine"></a>調整虛擬機器大小
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
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。
   
    您應該會看到虛擬機器的大小變更為 Standard_A1。

## <a name="add-a-data-disk-to-a-virtual-machine"></a>將資料磁碟新增至虛擬機器
這個範例示範如何將資料磁碟新增至執行中的虛擬機器。

1. 註解化您先前新增至 Main 方法的任何程式碼，但取得認證的程式碼除外。
2. 將下列方法新增至 Program 類別：
   
        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

## <a name="delete-a-virtual-machine"></a>刪除虛擬機器
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
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
4. 儲存 Program.cs 檔案。
5. 按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一組使用者名稱和密碼，登入 Azure AD。

## <a name="next-steps"></a>後續步驟
如果部署發生問題，請查看 [使用 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)




<!--HONumber=Nov16_HO3-->


