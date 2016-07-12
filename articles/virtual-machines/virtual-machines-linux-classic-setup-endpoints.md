<properties
	pageTitle="在傳統 Linux VM 上設定端點 | Microsoft Azure"
	description="了解如何在 Azure 傳統入口網站中設定端點，以允許與 Azure 中的 Linux 虛擬機器進行通訊"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# 如何在 Azure 中的傳統虛擬機器上設定端點

在 Azure 中使用傳統部署模型建立的所有 Linux 虛擬機器，都可以自動透過私人網路通道與同一雲端服務或虛擬網路中的其他虛擬機器通訊。不過，網際網路或其他虛擬網路上的電腦需要端點，才能將傳入網路流量導向至虛擬機器。本文也適用於 [Windows 虛擬機器](virtual-machines-windows-classic-setup-endpoints.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

當您在 Azure 傳統入口網站中建立 Linux 虛擬機器時，通常會自動為您建立安全殼層 (SSH) 的端點。建立虛擬機器或日後有需要時，您可以設定其他端點。
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## 後續步驟

* 您也可以使用 [Azure 命令列介面](../virtual-machines-command-line-tools.md)建立 VM 端點。執行 **azure vm endpoint create** 命令。

* 如果您已在 Resource Manager 部署模型中建立虛擬機器，您可以使用 Resource Manager 模式中的 Azure CLI 來[建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-cli.md)以控制 VM 的流量。

<!---HONumber=AcomDC_0629_2016-->