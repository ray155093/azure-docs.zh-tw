---
title: "Azure 虛擬機器 PowerShell 範例 | Microsoft Docs"
description: "Azure 虛擬機器 PowerShell 範例"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/16/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c57bf0efc1eb97d34d76483b8889d884ccfd0988
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 虛擬機器 PowerShell 範例

下表包含可供建立和管理 Windows 虛擬機器之 PowerShell 指令碼範例的連結。

| | |
|---|---|
|**建立虛擬機器**||
| [建立完整設定的虛擬機器](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立資源群組、虛擬機器和所有相關資源。|
| [建立 VM 並執行組態指令碼](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立虛擬機器，並使用 Azure 自訂指令碼擴充功能來安裝 IIS。 |
| [建立 VM 並執行 DSC 組態](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立虛擬機器，並使用 Azure 預期狀態設定 (DSC) 擴充功能來安裝 IIS。 |
| [從受控 OS 磁碟建立 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 連結現有受控磁碟作為 OS 磁碟，以建立虛擬機器。 |
| [從快照集建立 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 先從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，以從快照集建立虛擬機器。 |
|**監視虛擬機器**||
| [使用 Operations Management Suite 監視 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立虛擬機器、安裝 Operations Management Suite 代理程式，並在 OMS 工作區中註冊 VM。  |
| | |

