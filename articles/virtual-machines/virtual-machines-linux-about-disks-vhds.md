<properties
	pageTitle="有關 Linux VM 的磁碟和 VHD | Microsoft Azure"
	description="了解 Azure 中 Linux 虛擬機器的磁碟和 VHD 的基本知識。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# 有關 Azure 虛擬機器的磁碟和 VHD

就像任何其他電腦，Azure 中的虛擬機器會使用磁碟做為儲存作業系統、應用程式和資料的位置。所有 Azure 虛擬機器都至少有二個磁碟：Linux 作業系統磁碟 (如果是 Linux VM) 和暫存磁碟。作業系統磁碟是由映像建立，且作業系統磁碟與該映像，實際上都是儲存在 Azure 儲存體帳戶的虛擬硬碟 (VHD)。虛擬機器也可以有一或多個資料磁碟，而這些磁碟也會儲存成 VHD。本文也適用於 [Windows 虛擬機器](virtual-machines-windows-about-disks-vhds.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 後續步驟

-  [連接磁碟](virtual-machines-linux-attach-disk-portal.md)以為您的 VM 加入額外的儲存空間。
-  [設定軟體 RAID](virtual-machines-linux-configure-raid.md) 以提供備援性。
-  [擷取 Linux 虛擬機器](virtual-machines-linux-classic-capture-image.md)，讓您能快速部署額外的 VM。

<!---HONumber=AcomDC_0413_2016-->