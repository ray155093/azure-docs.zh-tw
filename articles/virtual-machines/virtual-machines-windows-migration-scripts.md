<properties
	pageTitle="從 Azure 服務管理移轉到 Azure Resource Manager 的社群工具"
	description="本文收錄由社群所提供，可協助將 IaaS 資源從 Azure 服務管理移轉至 Azure Resource Manager 堆疊的工具。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="singhkay"/>

# 從 Azure 服務管理移轉到 Azure Resource Manager 的社群工具

本文收錄由社群所提供，可協助將 IaaS 資源從 Azure 服務管理移轉至 Azure Resource Manager 堆疊的工具。

>[AZURE.NOTE]這些工具尚未獲得 Microsoft 支援服務的官方支援。因此可在 Github 上取得其開放原始碼，我們很樂意收到修正或其他案例的 PR。若要回報問題，請使用 Github 問題功能。
>
> 使用這些工具移轉可能會造成傳統虛擬機器停機。如果您想要尋求平台支援的移轉，請造訪
>
>- [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 堆疊的平台](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [深入技術探討支援從傳統移轉至 Azure Resource Manager 的平台](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## ASM2ARM

這是將您的**單一**虛擬機器 (VM) 從 Azure 服務管理堆疊移轉至 Azure Resource Manager 堆疊的 PowerShell 指令碼模組。

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## migAz

migAz 是將一組完整的 Azure 服務管理 IaaS 資源移轉至 Azure Resource Manager IaaS 資源的額外選項。移轉可能會發生在相同的訂用帳戶內或不同的訂用帳戶與訂用帳戶類型之間 (例如 CSP 訂用帳戶)。

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)

<!---HONumber=AcomDC_0831_2016-->