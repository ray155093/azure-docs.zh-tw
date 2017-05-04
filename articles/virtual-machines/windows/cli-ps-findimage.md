---
title: "瀏覽並選取 Windows VM 映像 | Microsoft Docs"
description: "了解如何在使用資源管理員部署模型建立 Windows 虛擬機器時，判斷映像的發行者、訂閱詳情及 SKU。"
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28bb214570fcca94c5ceb6071c4851b81ec00c8d
ms.lasthandoff: 04/27/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>使用 PowerShell 在 Azure 中瀏覽並選取 Windows 虛擬機器映像
本主題說明如何針對您可能在其中進行部署的每個位置，尋找 VM 映像發行者、提供項目、SKU 及版本。 例如，一些常用的 Windows VM 映像包括︰

## <a name="table-of-commonly-used-windows-images"></a>常用 Windows 映像表
| PublisherName | 提供項目 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>使用 PowerShell 來尋找 Azure 映像
> [!NOTE]
> 安裝及設定 [最新的 Azure PowerShell](/powershell/azure/overview)。 如果您使用 1.0 以下的 Azure PowerShell 模組，您仍可以使用下列命令，但您必須先 `Switch-AzureMode AzureResourceManager`。 
> 
> 

使用 Azure 資源管理員建立新的虛擬機器時，在某些情況下，您需要使用下列映像屬性組合來指定映像：

* 發佈者
* 提供項目
* SKU

例如， `Set-AzureRMVMSourceImage` PowerShell Cmdlet 或資源群組範本檔案需要這些值，而在此檔案中，您必須指定要建立的虛擬機器類型。

如果您需要決定這些值，則可以巡覽映像以決定這些值，方法是：

1. 列出映像發行者。
2. 針對指定的發行者，列出其提供項目。
3. 針對指定的提供項目，列出其 SKU。

首先，使用以下命令列出發行者：

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

填入您選擇的發行者名稱，然後執行以下命令：

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

填入您選擇的提供項目名稱，然後執行以下命令：

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

從 `Get-AzureRMVMImageSku` 命令的顯示中，您擁有指定新虛擬機器映像時所需的所有資訊。

下圖顯示完整範例：

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

針對 "MicrosoftWindowsServer" 發佈者：

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

針對 "WindowsServer" 提供項目：

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

從這個清單中，複製選擇的 SKU 名稱，您就可以取得 `Set-AzureRMVMSourceImage` PowerShell Cmdlet 或資源群組範本的所有資訊。

## <a name="next-steps"></a>後續步驟
現在，您可以精確地選擇想要使用的映像。 若要使用您剛找到的映像資訊來快速建立虛擬機器，或使用範本搭配該映像資訊，請參閱[使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

