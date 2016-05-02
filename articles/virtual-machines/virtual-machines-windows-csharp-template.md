<properties
	pageTitle="使用 C# 和 Resource Manager 範本部署 VM |Microsoft Azure"
	description="瞭解如何使用 C# 和 Resource Manager 範本來部署 Azure VM。"
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
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# 利用 C# 和 Resource Manager 範本來部署 Azure 虛擬機器

藉由使用資源群組和範本，您可以管理所有能夠一起支援您的應用程式的資源。本文說明如何使用 Azure PowerShell 設定驗證和儲存體，然後使用 C# 建立 Azure 資源以建立和部署範本。

若要完成本教學課程，您需要：

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [驗證權杖](../resource-group-authenticate-service-principal.md)

執行這些步驟需要 30 分鐘左右。

## 步驟 1：安裝 Azure PowerShell

有關如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。
    
## 步驟 2：建立範本儲存體的資源群組

所有資源都必須部署在資源群組中。如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)。

1. 取得可以建立資源的可用位置清單。

	    Get-AzureLocation | sort Name | Select Name

2. 使用清單中的位置 (例如**美國中部**) 取代 **$locName** 的值。建立變數。

        $locName = "location name"
        
3. 使用新資源群組的名稱取代 **$rgName** 的值。建立變數和資源群組。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    您應該會看到如下的結果：
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## 步驟 3︰建立儲存體帳戶和範本容器

需要儲存體帳戶才能儲存您即將建立及部署的範本。

1. 使用儲存體帳戶名稱取代 $stName 的值 (僅限小寫字母和數字)。測試名稱的唯一性。

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    如果這個命令傳回 **False**，表示您設定的名稱不重複。
    
2. 現在，請執行此命令來建立儲存體帳戶。
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. 使用帳戶中的 blob 儲存體端點來取代 {blob-storage-endpoint}。使用您的儲存體帳戶名稱來取代 {storage-account-name}。使用主要儲存體金鑰來取代 {primary-storage-key}。執行下列命令來建立儲存檔案的容器。您可以從 Azure 入口網站取得端點和金鑰值。

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## 步驟 3：建立 Visual Studio 專案、範本檔案，以及參數檔案

### 建立範本檔案

有了 Azure Resource Manager 範本之後，您就可以使用 JSON 來敘述資源、相關設定和部署參數，一起部署和管理 Azure 資源。您在本教學課程中建立的範本，類似於資源庫中提供的範本。如需深入了解範本，請參閱[在美國西部部署簡單的 Windows VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/)。

在 Visual Studio 中，執行這項操作︰

1. 按一下 [檔案] > [新增] > [專案]。

2. 在 [**範本**] > [**Visual C#**] 中，選取 [**主控台應用程式**]，輸入專案的名稱和位置，然後按一下 [**確定**]。

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [**加入**] > [**新增項目**]。

4. 按一下 [Web]，選取 JSON 檔案，輸入 VirtualMachineTemplate.json 為名稱，然後按一下 [新增]。

5. 在 VirtualMachineTemplate.json 檔案的開頭和結尾括號中，加入必要的結構描述元素和必要的 contentVersion 元素︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [參數](../resource-group-authoring-templates.md#parameters)並非必要項目，但是可以讓範本管理更容易。它們會說明值類型、預設值 (如有需要)，以及可能的參數允許值。對於此教學課程，用來建立虛擬機器、儲存體帳戶和虛擬網路的參數會加入至範本。在 ContentVersion 元素之後加入參數元素及其子元素：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. [變數](../resource-group-authoring-templates.md#variables)可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。在參數區段之後加入變數元素：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. [資源](../resource-group-authoring-templates.md#resources)，例如虛擬機器、虛擬網路，以及儲存體帳戶接下來會在範本中定義。在變數區段之後加入資源區段：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. 儲存您所建立的範本檔案。

### 建立參數檔案

若要為範本中所定義的資源參數指定值，您可以建立參數檔案，其中包含值並且將其提交至具有範本的資源管理員。在 Visual Studio 中，執行這項操作︰

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後依序按一下 [**加入**]、[**新增項目**]。

2. 按一下 [Web]，選取 JSON 檔案，在 [名稱] 中輸入 Parameters.json，然後按一下 [新增]。

3. 開啟 parameters.json 檔案，然後加入此 JSON 內容：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] 本文會建立執行 Windows Server 作業系統版本的虛擬機器。若要深入了解如何選取其他映像，請參閱[使用 Windows PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

4. 儲存您所建立的參數檔案。

### 上傳檔案並設定其使用權限 

範本檔案和參數檔案可由 Azure 資源管理員從 Azure 儲存體帳戶存取。若要在您建立的第一個儲存體中置入檔案，請執行此動作：

1. 開啟 [雲端總管]，然後瀏覽至您稍早建立的儲存體帳戶中的範本容器。

2. 在範本容器視窗右上角按一下 [上傳 Blob] 圖示，瀏覽至您建立的 VirtualMachineTemplate.json 檔案，然後按一下 [開啟]。

3. 再按一下 [上傳 Blob] 圖示，瀏覽至您建立的 Parameters.json 檔案，然後按一下 [開啟]。

## 步驟 4：安裝程式庫

NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。您必須安裝 Azure Resource Management Library 和Azure Active Directory Authentication Library。若要在 Visual Studio 中取得這些程式庫，請執行此動作：

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]。

2. 在搜尋方塊中輸入 *Active Directory*，對 Active Directory Authentication Library 封裝按一下 [**安裝**]，然後依照指示，安裝封裝。

4. 在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 Microsoft.Azure.ResourceManager，對 Microsoft Azure Resource Management Libraries 按一下 [安裝]，然後依照指示安裝封裝。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

##步驟 5：建立用來驗證要求的認證

已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您現在要將應用程式資訊格式化成用來驗證對 Azure Resource Manager 的要求的認證。執行此動作：

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	將下列方法新增至 Program 類別，以取得建立認證所需的權杖：

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    將 {application-id} 取代成您先前記錄的應用程式識別碼、將 {password} 取代成您為 AD 應用程式選擇的密碼，並將 {tenant-id} 取代成您的訂用帳戶的租用戶識別碼。您可以透過執行 Get-AzureRmSubscription 來尋找租用戶識別碼。

3. 將下列程式碼新增至 Program.cs 檔案的 Main 方法以建立認證：

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. 儲存 Program.cs 檔案。

## 步驟 6：加入程式碼以部署範本

在此步驟中，您可以使用 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 和 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 類別建立要在其中部署資源的資源群組。

1. 將變數新增至 Program 類別的 Main 方法，以指定您要用於資源的名稱、資源的位置 (例如「美國中部」)、系統管理員帳戶資訊，以及您的訂用帳戶識別碼：

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    以您想要使用的名稱和識別碼取代所有變數值。您可以執行 Get-AzureRmSubscription 來尋找訂用帳戶識別碼。StorageName 變數的值是儲存範本的儲存體帳戶的名稱。
    
2. 將下列方法新增至 Program 類別以建立資源群組：

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. 將此方法加入至 Program 類別，以使用您所定義的範本，將資源部署至資源群組：

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. 將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName}",
          subscriptionId}");
        Console.ReadLine();

##步驟 7：加入程式碼以刪除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。您不需要從資源群組個別刪除每個資源。只要刪除資源群組，其所有資源都將會自動刪除。

1.	將下列方法新增至 Program 類別以刪除資源群組：

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	將下列程式碼新增至 Main 方法，以呼叫您剛才新增的方法：

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##步驟 8：執行主控台應用程式

1.	若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]，然後使用搭配您的訂用帳戶使用的相同使用者名稱和密碼，登入 Azure AD。

2.	在顯示 [已接受] 狀態之後按 **Enter** 鍵。

	此主控台應用程式從開始到完成的完整執行應該需要 5 分鐘左右。在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure 入口網站中建立的資源，然後再刪除。

3. 瀏覽至 Azure 入口網站中的稽核記錄，以查看資源的狀態：

	![在 Azure 入口網站中瀏覽稽核記錄檔](./media/virtual-machines-windows-csharp-template/crpportal.png)

## 後續步驟

- 如果部署有問題，下一個步驟是查看[透過 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)
- 請參閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)，了解如何管理您剛才建立的虛擬機器。

<!---HONumber=AcomDC_0420_2016-->