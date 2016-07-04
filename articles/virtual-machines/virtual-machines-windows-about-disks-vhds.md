<properties
	pageTitle="有關 Windows VM 的磁碟和 VHD | Microsoft Azure"
	description="了解 Azure 中 Windows 虛擬機器的磁碟和 VHD 的基本知識。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# 有關 Azure 虛擬機器的磁碟和 VHD

就像任何其他電腦，Azure 中的虛擬機器會使用磁碟做為儲存作業系統、應用程式和資料的位置。所有 Azure 虛擬機器都至少有二個磁碟：Windows 作業系統磁碟 (如果是 Windows VM) 和暫存磁碟。作業系統磁碟是由映像建立，且作業系統磁碟與該映像，實際上都是儲存在 Azure 儲存體帳戶的虛擬硬碟 (VHD)。虛擬機器也可以有一或多個資料磁碟，而這些磁碟也會儲存成 VHD。本文也適用於 [Linux 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 後續步驟
-  [擷取 Windows 虛擬機器](virtual-machines-windows-capture-image.md)，讓您可以向外延展您的 VM 部署。
-  [將 Windows VM 映像上傳至 Azure](virtual-machines-windows-upload-image.md)，以在建立新的 VM 時使用。
-  [變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md)，讓您的應用程式可以使用 D: 磁碟機的資料。

<!---HONumber=AcomDC_0622_2016-->