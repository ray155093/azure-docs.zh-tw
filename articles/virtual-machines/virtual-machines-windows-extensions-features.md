---
title: 虛擬機器擴充功能和功能 | Microsoft Docs
description: 了解哪些擴充功能適用於 Azure 虛擬機器，並依它們提供或改善的內容來分組。
services: virtual-machines-windows
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters

---
# <a name="about-virtual-machine-extensions-and-features"></a>有關虛擬機器擴充功能和功能
## <a name="azure-vm-extensions"></a>Azure VM 擴充功能
「Azure 虛擬機器」擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 例如，如果「虛擬機器」要求安裝軟體、防毒保護或 Docker 組態，便可使用 VM 擴充功能來完成這些工作。 您可以使用 Azure CLI、PowerShell、Resource Manager 範本及 Azure 入口網站來執行 Azure VM 擴充功能。 擴充功能可以與新的虛擬機器部署搭配，或是在任何現有的系統上執行。

本文件提供「Azure 虛擬機器」擴充功能的必要條件，以及如何偵測可用 VM 擴充功能的指引。 

## <a name="azure-vm-agent"></a>Azure VM 代理程式
「Azure VM 代理程式」可管理「Azure 虛擬機器」與「Azure 網狀架構控制器」之間的互動。 VM 代理程式負責部署和管理「Azure 虛擬機器」的許多功能層面，包括執行「VM 擴充功能」。 「Azure VM 代理程式」會預先安裝在「Azure 資源庫映像」上，並可安裝在支援的作業系統上。 

如需有關支援的作業系統和安裝指示，請參閱 [Azure 虛擬機器代理程式](virtual-machines-windows-classic-agents-and-extensions.md)。

## <a name="discover-vm-extensions"></a>探索 VM 擴充功能
有許多不同的 VM 擴充功能可供與「Azure 虛擬機器」搭配使用。 若要查看完整清單，請使用 Azure CLI 來執行下列命令，其中將位置取代成所選擇的位置。

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>常見的 VM 擴充功能
| 擴充功能名稱 | 說明 | 相關資訊 |
| --- | --- | --- |
| Windows 的自訂指令碼延伸模組 |對「Azure 虛擬機器」執行指令碼 |[Windows 的自訂指令碼延伸模組](virtual-machines-windows-extensions-customscript.md) |
| Windows 的 DSC 延伸模組 |PowerShell DSC (所需的狀態組態) 延伸模組。 |[Docker VM 擴充功能](virtual-machines-windows-extensions-dsc-overview.md) |
| Azure 診斷擴充功能 |管理「Azure 診斷」 |[Azure 診斷擴充功能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

<!--HONumber=Oct16_HO2-->


