---
title: "Azure CLI 範例 Windows | Microsoft Docs"
description: "Azure CLI 範例 Windows"
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
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: cde57732f16d9571af8cc4ae5df3c012579ce831
ms.openlocfilehash: 29e8a19d4c05ac3c01ecbe43650c6607234c0546
ms.lasthandoff: 03/01/2017


---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>適用於 Windows 虛擬機器的 Azure CLI 範例

下表包含使用 Azure CLI 所建置、可部署 Windows 虛擬機器之 Bash 指令碼的連結。

| | |
|---|---|
|**建立虛擬機器**||
| [建立虛擬機器](./scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 以最低限度的組態建立 Windows 虛擬機器。 |
| [建立完整設定的虛擬機器](./scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 建立資源群組、虛擬機器和所有相關資源。|
| [建立高可用性的虛擬機器](./scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 依據高可用性和負載平衡組態建立數個虛擬機器。 |
| [建立 VM 並執行組態指令碼](./scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 建立虛擬機器，並使用 Azure 自訂指令碼擴充功能來安裝 IIS。 |
| [建立 VM 並執行 DSC 組態](./scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 建立虛擬機器，並使用 Azure 預期狀態設定 (DSC) 擴充功能來安裝 IIS。 |
|**網路虛擬機器**||
| [保護虛擬機器之間的網路流量](./scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 建立兩部虛擬機器、所有相關資源以及內部和外部網路安全性群組 (NSG)。 |
|**監視虛擬機器**||
| [使用 Operations Management Suite 監視 VM](./scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 建立虛擬機器、安裝 Operations Management Suite 代理程式，並在 OMS 工作區中註冊 VM。  |
| | |

