---
title: "使用 Resource Manager 和 PowerShell 管理 VM | Microsoft Docs"
description: "使用 Azure Resource Manager 和 PowerShell 管理虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: b54a95c4f81d9a981912e1b596a817dc6ad56334
ms.openlocfilehash: 52684fe3212454abbfb0cf9d1c67759fce9a1549


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>使用 Resource Manager 和 PowerShell 管理 Azure 虛擬機器
## <a name="install-azure-powershell"></a>安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="set-variables"></a>設定變數
本文中的所有命令都需要虛擬機器所在資源群組的名稱以及要管理之虛擬機器的名稱。 使用包含虛擬機器之資源群組的名稱取代 **$rgName** 的值。 使用 VM 名稱取代 **$vmName** 的值。 建立變數。

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>顯示虛擬機器的相關資訊
取得虛擬機器資訊。

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

它會傳回類似下列的範例：

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>停止虛擬機器
停止執行中的虛擬機器。

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

系統會要您確認：

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

輸入 **Y** 停止虛擬機器。

數分鐘之後，它會傳回類似此範例：

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>啟動虛擬機器
如果已停止，啟動虛擬機器。

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

數分鐘之後，它會傳回類似此範例：

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

如果您想要重新啟動已在執行中虛擬機器，請使用 **重新啟動 AzureRmVM** 一節所說明。

## <a name="restart-a-virtual-machine"></a>重新啟動虛擬機器
重新啟動執行中的虛擬機器。

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

它會傳回類似下列的範例：

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>刪除虛擬機器
刪除虛擬機器。  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> 您可以使用 **– Force** 參數，略過確認提示。
> 
> 

如果您沒有使用 -Force 參數，系統會要您確認：

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

它會傳回類似下列的範例：

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>更新虛擬機器
此範例示範如何更新虛擬機器的大小。

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

它會傳回類似下列的範例：

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

如需虛擬機器的可用大小清單，請參閱 [Azure 中的虛擬機器大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 。


## <a name="next-steps"></a>後續步驟
如果部署有問題，您可以查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解 (英文)](../azure-resource-manager/resource-manager-common-deployment-errors.md)




<!--HONumber=Feb17_HO2-->


