<properties
	pageTitle="管理虛擬機器擴展集中的 VM | Microsoft Azure"
	description="使用 Azure PowerShell 管理虛擬機器擴展集中的虛擬機器。"
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
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# 管理虛擬機器擴展集中的虛擬機器

您可以使用本文中的工作來管理虛擬機器擴展集中的虛擬機器資源。

所有涉及管理擴展集中虛擬機器的工作，都需要您知道要管理的電腦執行個體識別碼。您可以使用 [Azure 資源總管](https://resources.azure.com)尋找擴展集中虛擬機器的執行個體識別碼。您也可以使用資源總管來確認您所完成的工作狀態。

如需如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，並選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。

## 顯示虛擬機器擴展集的相關資訊

您可以取得擴展集，也稱為執行個體檢視的一般資訊。或者，您可以取得更特定的資訊，如集合中的資源資訊。

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*，並以虛擬機器擴展集的名稱取代*擴展集名稱*，然後執行命令：

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

它會傳回類似下列畫面：

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以虛擬機器擴展集的名稱取代*擴展集名稱*，並以您要取得相關資訊之虛擬機器的執行個體識別碼取代 *#*，然後執行命令：

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
它會傳回類似下列畫面：

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
## 啟動擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要啟動的虛擬機器識別碼取代 *#*，然後執行命令：

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在資源總管中，我們可以看到執行個體的狀態是**執行中**：

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

您可以啟動集合中的所有虛擬機器，只要不使用 -InstanceId 參數即可。
    
## 停止擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要停止的虛擬機器識別碼取代 *#*，然後執行命令：

	Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在資源總管中，我們可以看到執行個體的狀態是**解除配置**：

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
若要停止虛擬機器但不解除配置，請使用 -StayProvisioned 參數。您可以停止集合中的所有虛擬機器，只要不使用 -InstanceId 參數即可。
    
## 重新啟動擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要重新啟動的虛擬機器識別碼取代 *#*，然後執行命令：

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
您可以重新啟動集合中的所有虛擬機器，只要不使用 -InstanceId 參數即可。

## 移除擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要從擴展集中移除的虛擬機器識別碼取代 *#*，然後執行命令：

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

您可以一次移除虛擬機器擴展集，只要不使用 -InstanceId 參數即可。

<!---HONumber=AcomDC_0504_2016-->