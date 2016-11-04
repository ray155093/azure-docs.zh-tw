---
title: 使用 C# 和 Resource Manager 範本部署 VM | Microsoft Docs
description: 了解如何使用 C# 和 Resource Manager 範本來部署 Azure VM。
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu

---
# <a name="deploy-an-azure-virtual-machine-using-c#-and-a-resource-manager-template"></a>利用 C# 和 Resource Manager 範本來部署 Azure 虛擬機器
藉由使用資源群組和範本，您可以管理所有能夠支援您的應用程式的資源。 本文說明如何使用 Visual Studio 和 C# 設定驗證、建立範本，然後使用您所建立的範本來部署 Azure 資源。

您必須先確定已完成下列設定步驟：

* 安裝 [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* 驗證 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) 的安裝
* 取得 [驗證權杖](../resource-group-authenticate-service-principal.md)
* 使用 [Azure PowerShell](../resource-group-template-deploy.md)、[Azure CLI](../resource-group-template-deploy-cli.md) 或 [Azure 入口網站](../resource-group-template-deploy-portal.md)建立資源群組。

執行這些步驟需要 30 分鐘左右。

## <a name="step-1:-create-the-visual-studio-project,-the-template-file,-and-the-parameters-file"></a>步驟 1：建立 Visual Studio 專案、範本檔案，以及參數檔案
### <a name="create-the-template-file"></a>建立範本檔案
有了 Azure Resource Manager 範本之後，您就可以一起部署和管理 Azure 資源。 範本是資源的 JSON 描述和相關聯的部署參數。

在 Visual Studio 中，執行下列操作：

1. 按一下 [檔案] > [新增] > [專案]。
2. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式]，輸入專案的名稱和位置，然後按一下 [確定]。
3. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [新增] > [新增項目]。
4. 按一下 [Web]，選取 JSON 檔案，為名稱輸入 *VirtualMachineTemplate.json*，然後按一下 [新增]。
5. 在 VirtualMachineTemplate.json 檔案的開頭和結尾括號中，加入必要的結構描述元素和必要的 contentVersion 元素︰
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [參數](../resource-group-authoring-templates.md#parameters)並非總是必要的項目，但它們能提供在部署範本時輸入值的方式。 在 ContentVersion 元素之後加入參數元素及其子元素：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [變數](../resource-group-authoring-templates.md#variables) 可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。 在參數區段之後加入變數元素：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [資源](../resource-group-authoring-templates.md#resources) ，例如虛擬機器、虛擬網路，以及儲存體帳戶接下來會在範本中定義。 在變數區段之後加入資源區段：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. 儲存您所建立的範本檔案。

### <a name="create-the-parameters-file"></a>建立參數檔案
若要為範本中所定義的資源參數指定值，可建立參數檔案，其中包含部署範本時使用的值。 在 Visual Studio 中，執行下列操作：

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [新增] > [新增項目]。
2. 按一下 [Web]，選取 JSON 檔案，為名稱輸入 *Parameters.json*，然後按一下 [新增]。
3. 開啟 parameters.json 檔案，然後加入此 JSON 內容：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > 本文會建立執行 Windows Server 作業系統版本的虛擬機器。 若要深入了解如何選取其他映像，請參閱 [使用 Windows PowerShell 和 Azure CLI 巡覽並選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。
   > 
   > 
4. 儲存您所建立的參數檔案。

## <a name="step-2:-install-the-libraries"></a>步驟 2：安裝程式庫
NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。 您必須安裝 Azure Resource Management Library 和 Azure Active Directory Authentication Library 以建立資源。 若要在 Visual Studio 中取得這些程式庫，請執行下列步驟：

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [管理 NuGet 封裝]，然後按一下 [瀏覽]。
2. 在搜尋方塊中輸入 *Active Directory* ，對 Active Directory Authentication Library 封裝按一下 [ **安裝** ]，然後依照指示，安裝封裝。
3. 在頁面的頂端，選取 [包含發行前版本] 。 在搜尋方塊中輸入 *Microsoft.Azure.Management.ResourceManager*，對 Microsoft Azure Resource Management Library 按一下 [安裝]，然後依照指示安裝封裝。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

## <a name="step-3:-create-the-credentials-that-are-used-to-authenticate-requests"></a>步驟 3：建立用來驗證要求的認證
已建立 Azure Active Directory 應用程式，而且已安裝驗證程式庫。 現在您可以將應用程式資訊格式化為用來向 Azure Resource Manager 驗證要求的認證。

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端：
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. 將下列方法新增至 Program 類別，以取得建立認證所需的權杖：
   
       private static async Task<AuthenticationResult> GetAccessTokenAsync()
       {
         var cc = new ClientCredential("{client-id}", "{client-secret}");
         var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
         var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
         if (token == null)
         {
           throw new InvalidOperationException("Could not get the token.");
         }
         return token;
       }
   
   將 {client-id} 用 Azure Active Directory 應用程式的識別碼取代，將 {client-secret} 用 AD 應用程式的存取金鑰取代，並將 {tenant-id} 用您訂用帳戶的租用戶識別碼取代。 您可以透過執行 Get-AzureRmSubscription 來尋找租用戶識別碼。 您可以使用 Azure 入口網站尋找存取金鑰。
3. 若要建立認證，請將此程式碼新增至 Program.cs 檔案中的 Main 方法：
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. 儲存 Program.cs 檔案。

## <a name="step-4:-deploy-the-template"></a>步驟 4：部署範本
在此步驟中，您可以使用先前建立的資源群組，也可以使用 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 和 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 類別建立資源群組。

1. 將變數新增至 Program 類別的 Main 方法，以指定您先前建立之資源的名稱、部署名稱，以及您的訂用帳戶識別碼：
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";
   
    使用您的資源群組名稱取代 groupName 的值。 使用您要用於部署的名稱取代 deploymentName 的值。 您可以執行 Get-AzureRmSubscription 來尋找訂用帳戶識別碼。
2. 將此方法加入至 Program 類別，以使用您所定義的範本，將資源部署至資源群組：
   
        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }
   
    如果您要從儲存體帳戶部署範本，您可以使用 TemplateLink 屬性取代 Template 屬性。
3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5:-delete-the-resources"></a>步驟 5：刪除資源
您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。 您不需要從資源群組個別刪除每個資源。 刪除資源群組，其所有資源都將會自動刪除。

1. 若要刪除資源群組，請將下列方法新增至 Program 類別：
   
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
2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：
   
       DeleteResourceGroupAsync(
         credential,
         groupName,
         subscriptionId);
       Console.ReadLine();

## <a name="step-6:-run-the-console-application"></a>步驟 6：執行主控台應用程式
1. 若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一個認證，登入 Azure AD。
2. 在顯示 [已接受] 狀態之後按 **Enter** 鍵。
   
   此主控台應用程式從開始到完成的完整執行應該需要五分鐘左右。 在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure 入口網站中建立的資源，然後再刪除。
3. 若要查看資源的狀態，請瀏覽至 Azure 入口網站中的稽核記錄：
   
    ![在 Azure 入口網站中瀏覽稽核記錄檔](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>後續步驟
* 如果部署有問題，下一個步驟就是查看[使用 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)。
* 檢閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-csharp-manage.md)，以了解如何管理您建立的虛擬機器。

<!--HONumber=Oct16_HO2-->


