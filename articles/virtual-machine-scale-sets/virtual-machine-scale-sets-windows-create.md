<properties
	pageTitle="建立虛擬機器調整集 | Microsoft Azure"
	description="使用 Powershell 建立虛擬機器擴展集"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 使用 Azure PowerShell 建立 Windows 虛擬機器擴展集

這些步驟會遵循填空方法建立 Azure 虛擬機器擴展集。整篇文章都是需要您提供值的變數。請使用對您的訂用帳戶和應用程式有意義的值取代括弧內的所有內容。

## 步驟 1：安裝 Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## 步驟 2：設定您的訂用帳戶

1. 開啟 PowerShell 提示字元。

2. 登入您的帳戶：

        Login-AzureRmAccount

3. 取得訂用帳戶：

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. 設定目前要使用的訂用帳戶：

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## 步驟 2：建立資源

建立新的虛擬機器擴展集所需要的資源。

### 資源群組

虛擬機器擴展集必須包含在資源群組中。

1.  執行這個命令取得可用位置和支援服務的清單︰

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    您應該會看到如下的結果

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    挑選最適合您的位置，然後使用該位置的名稱取代引號內的文字︰

        $locName = "location name from the list, such as Central US"

4. 以您要使用的新資源群組名稱取代引號內的文字，然後在您之前設定的位置建立它︰

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    您應該會看到如下的結果：

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### 儲存體帳戶

在擴展集中建立的虛擬機器需要儲存體帳戶來儲存相關聯的磁碟。

1. 以您要使用的儲存體帳戶名稱取代引號內的文字，然後測試它是否為唯一的名稱︰

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    如果答案是 **False**，表示您設定的名稱是唯一的。

2. 以儲存體帳戶的類型取代引號內的文字，然後使用您之前設定的名稱和位置建立帳戶。可能的值為︰Standard\_LRS、Standard\_GRS、Standard\_RAGRS 或 Premium\_LRS。

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    您應該會看到如下的結果：

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### 虛擬網路

擴展集中的虛擬機器需要虛擬網路。

1. 以您要使用的虛擬網路子網路名稱取代引號內的文字，然後建立組態︰

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. 以您要使用的虛擬網路名稱取代引號內的文字，然後使用您之前定義的資訊和資源建立它︰

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 公用 IP 位址

您必須先建立公用 IP 位址，再建立網路介面。

1. 以您要和公用 IP 位址一起使用的網域名稱標籤取代引號內的文字，然後測試它是否是唯一的名稱。標籤只能包含字母、數字和連字號，而最後一個字元必須是字母或數字︰

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    如果答案是 **False**，表示您設定的名稱是唯一的。

2. 以您要使用的公用 IP 位址名稱取代引號內的文字，然後建立它︰

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### 網路介面

有了公用 IP 位址之後，您就可以建立網路介面。

1. 以您要使用的網路介面名稱取代引號內的文字，然後使用您之前建立的資源建立它︰

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### 建立虛擬機器擴展集

您已有所需的全部資源，可以建立擴展集了。

1. 以您要使用的 IP 組態名稱取代引號內的文字，然後建立它︰

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. 以您要使用的擴展集組態名稱取代引號內的文字，然後建立它。這個步驟包括設定集合中的虛擬機器大小 (稱為 SkuName)。請參閱 [Azure 中的虛擬機器大小](..\virtual-machines\virtual-machines-windows-sizes.md)找出符合您需求的大小。本例中建議使用 Standard\_A0.：

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    您應該會看到如下的結果：

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. 以您要使用的電腦名稱前置詞、虛擬機器上的系統管理員帳戶名稱和帳戶密碼取代引號內的文字，然後建立作業系統設定檔︰

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    您應該會在 osProfile 區段中看到如下的結果：

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. 以您要使用的儲存體設定檔名稱、映像資訊名稱和儲存虛擬機器磁碟的儲存體路徑名稱取代引號內的文字，然後建立設定檔。請參閱[使用 CLI 或 PowerShell 在 Azure 中巡覽並選取 Linux 虛擬機器映像](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md)尋找您需要的資訊：

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    您應該會在 storageProfile 區段中看到如下的結果：

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. 以虛擬機器擴展集名稱取代引號內的文字，然後建立它︰

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    您應該會看到如下的結果，告訴您部署成功︰

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## 步驟 3︰瀏覽資源

請使用這些資源瀏覽您剛剛建立的虛擬機器擴展集︰

- Azure 入口網站 - 使用入口網站可以取得的有限資訊。
- [Azure 資源總管](https://resources.azure.com/) - 這是瀏覽擴展集目前狀態的最佳工具。
- Azure PowerShell - 使用這個命令取得資訊：

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## 後續步驟

1. 若要深入了解，請參閱 [Virtual Machine Scale Sets Overview](virtual-machine-scale-sets-overview.md) (虛擬機器擴展集概觀)。

2. 請考慮使用 [Automatic scaling and virtual machine scale sets](virtual-machine-scale-sets-autoscale-overview.md) (自動調整和虛擬機器擴展集) 中的資訊設定自動調整擴展集。

<!---HONumber=AcomDC_0427_2016-->