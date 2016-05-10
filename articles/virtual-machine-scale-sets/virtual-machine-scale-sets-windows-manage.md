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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 管理虛擬機器擴展集中的虛擬機器

管理 Microsoft Azure 中的資源時，Azure PowerShell 可提供您許多功能和彈性。您可以使用本文中的工作來管理虛擬機器擴展集中的虛擬機器資源。

- [顯示虛擬機器擴展集的相關資訊](#displayvm)
- [啟動擴展集中的虛擬機器](#start)
- [停止擴展集中的虛擬機器](#stop)
- [重新啟動擴展集中的虛擬機器](#restart)
- [刪除擴展集中的虛擬機器](#delete)

所有涉及管理擴展集中虛擬機器的工作，都需要您知道要管理的電腦執行個體識別碼。您可以使用 [Azure 資源總管](https://resources.azure.com)尋找擴展集中虛擬機器的執行個體識別碼。您也可以使用資源總管來確認您所完成的工作狀態。

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>顯示虛擬機器擴展集的相關資訊

您可以取得擴展集，也稱為執行個體檢視的一般資訊。或者，您可以取得更特定的資訊，如集合中的資源資訊。

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*，並以虛擬機器擴展集的名稱取代*擴展集名稱*，然後執行命令：

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

它會傳回類似下列畫面：

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

若要取得一般資訊，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*，並以虛擬機器擴展集的名稱取代*擴展集名稱*，然後執行命令：

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

它會傳回類似下列畫面：

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>啟動擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*VM 擴展集名稱*，並以您要重新啟動的虛擬機器識別碼取代*執行個體識別碼*，然後執行命令：

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="stop"></a>停止擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要停止的虛擬機器識別碼取代*執行個體識別碼*，然後執行命令：

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

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

## <a id="restart"></a>重新啟動擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要重新啟動的虛擬機器識別碼取代*執行個體識別碼*，然後執行命令：

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>移除擴展集中的虛擬機器

在這個命令中，以包含虛擬機器擴展集的資源群組名稱取代*資源群組名稱*、以擴展集的名稱取代*擴展集名稱*，並以您要從擴展集中移除的虛擬機器識別碼取代*執行個體識別碼*，然後執行命令：

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->