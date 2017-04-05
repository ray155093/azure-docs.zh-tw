---
title: "社群工具 - 將傳統資源移到 Azure Resource Manager | Microsoft Docs"
description: "本文收錄由社群所提供、可協助將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 部署模型的工具。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5e2ad5e5eae97645368797c8cdf848d88719bb64
ms.lasthandoff: 03/29/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>可將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 的社群工具
本文收錄由社群所提供、可協助將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 部署模型的工具。

> [!NOTE]
> 這些工具尚未獲得 Microsoft 支援服務的官方支援。 因此可在 GitHub 上取得其開放原始碼，我們很樂意收到修正或其他案例的 PR。 若要回報問題，請使用 GitHub 問題功能。
> 
> 使用這些工具移轉可能會造成傳統虛擬機器停機。 如果您想要尋求平台支援的移轉，請造訪 
> 
> * [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 堆疊的平台](virtual-machines-windows-migration-classic-resource-manager.md)
> * [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
這是一個 PowerShell 指令碼模組，可將您的「單一」虛擬機器 (VM) 從傳統模型移轉至 Azure Resource Manager 部署模型。 

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz 是可將一整組傳統 IaaS 資源移轉至 Azure Resource Manager IaaS 資源的額外選項。 移轉可能會發生在相同的訂用帳戶內或不同的訂用帳戶與訂用帳戶類型之間 (例如 CSP 訂用帳戶)。

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)


