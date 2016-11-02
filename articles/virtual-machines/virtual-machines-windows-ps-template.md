<properties
    pageTitle="使用 Resource Manager 範本建立 VM | Microsoft Azure"
    description="使用 Resource Manager 範本和 PowerShell 輕鬆地建立新的 Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>利用 Resource Manager 範本建立 Windows 虛擬機器

本文介紹 Azure Resource Manager 範本，並說明如何使用 PowerShell 進行部署。 此範本會在具有單一子網路的新虛擬網路上，部署執行 Windows Server 的單一虛擬機器。

執行本文中的步驟約需 20 分鐘的時間。

> [AZURE.IMPORTANT] 如果您希望 VM 屬於可用性設定組的一部分，可在建立 VM 時將其加入設定組。 目前不支援在建立 VM 之後，再將其加入可用性設定組。

## <a name="step-1:-create-the-template-file"></a>步驟 1：建立範本檔案

您可以使用 [編寫 Azure Resource Manager 範本](../resource-group-authoring-templates.md)中的資訊來建立專屬範本。 您也可以從 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)部署已為您建立的範本。

1. 開啟您慣用的文字編輯器，並新增必要的結構描述項目和必要的 contentVersion 元素︰

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [參數](../resource-group-authoring-templates.md#parameters)並非總是必要的項目，但它們能提供在部署範本時輸入值的方式。 在 ContentVersion 元素之後加入參數元素及其子元素：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [變數](../resource-group-authoring-templates.md#variables) 可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。 在參數區段之後加入變數元素：

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
        
4. [資源](../resource-group-authoring-templates.md#resources) ，例如虛擬機器、虛擬網路，以及儲存體帳戶接下來會在範本中定義。 在變數區段之後加入資源區段：

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
          
    >[AZURE.NOTE] 本文會建立執行 Windows Server 作業系統版本的虛擬機器。 若要深入了解如何選取其他映像，請參閱 [使用 Windows PowerShell 和 Azure CLI 來瀏覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。  
            
2. 將範本檔案另存為 *VirtualMachineTemplate.json*。

## <a name="step-2:-create-the-parameters-file"></a>步驟 2︰建立參數檔案

若要為範本中所定義的資源參數指定值，可建立參數檔案，其中包含部署範本時使用的值。

1. 在文字編輯器中，將此 JSON 內容複製到名為 *Parameters.json*的新檔案中：

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] 進一步了解 [使用者名稱和密碼需求](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)。

2. 儲存參數檔案。

## <a name="step-3:-install-azure-powershell"></a>步驟 3：安裝 Azure PowerShell

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## <a name="step-4:-create-a-resource-group"></a>步驟 4：建立資源群組

所有資源都必須部署在[資源群組](../resource-group-overview.md)中。

1. 取得可以建立資源的可用位置清單。

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. 使用清單中的位置 (例如**美國中部**) 取代 **$locName** 的值。 建立變數。

        $locName = "location name"
        
3. 使用新資源群組的名稱取代 **$rgName** 的值。 建立變數和資源群組。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    您應該會看到如下列範例的結果：
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>步驟 5 ︰使用範本和參數來建立資源

使用範本檔案的路徑和名稱取代 **$templateFile** 的值。 使用參數檔案的路徑和名稱取代 **$parameterFile** 的值。 建立變數，然後部署範本。 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] 您也可以從 Azure 儲存體帳戶部署範本和參數。 若要深入了解，請參閱[搭配使用 Azure PowerShell 與 Azure 儲存體](../storage/storage-powershell-guide-full.md)。

## <a name="next-steps"></a>後續步驟

- 如果部署有問題，下一個步驟就是查看 [使用 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)
- 檢閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)，以了解如何管理您建立的虛擬機器。



<!--HONumber=Oct16_HO2-->


