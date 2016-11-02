<properties
	pageTitle="什麼是 VM 擴展集？ | Microsoft Azure"
	description="了解 VM 擴展集。"
	keywords="linux 虛擬機器, 虛擬機器擴展集" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2016"
	ms.author="gatneil"/>

# 什麼是虛擬機器擴展集？

虛擬機器擴展集可讓您以一個集合來管理多部 VM。概括而言，擴展集具有下列優缺點︰

優點：

1. 高可用性。每個擴展集都會將它的 VM 放入具有 5 個容錯網域 (FD) 和 5 個更新網域 (UD) 的可用性設定組，以確保可用性 (如需 FD 和 UD 的詳細資訊，請參閱 [VM 可用性](./virtual-machines-linux-manage-availability.md))。 
2. 與 Azure 負載平衡器和應用程式閘道輕鬆整合。
3. 與 Azure 自動調整輕鬆整合。
4. 簡化 VM 的部署、管理和清除。
5. 支援常見 Windows 和 Linux 類別以及自訂映像。

缺點：

1. 無法將資料磁碟連接至擴展集中的 VM 執行個體。相反地，必須使用 Blob 儲存體、Azure 檔案、Azure 資料表或其他儲存體解決方案。

## 使用 Azure CLI 快速建立

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## 後續步驟

如需一般資訊，請參閱[擴展集的主要登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

如需文件，請參閱[擴展集的主要文件頁面](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

如需使用擴展集的範例 Resource Manager 範本，請在 [Azure 快速入門範本 github 儲存機制](https://github.com/Azure/azure-quickstart-templates)中搜尋 "vmss"。

<!---HONumber=AcomDC_0413_2016-->