<properties
	pageTitle="Linux VM 的常見問題集 | Microsoft Azure"
	description="針對以 Resource Manager 模型建立的 Linux 虛擬機器，提供一些相關常見問題的解答。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Linux 虛擬機器的常見問題 


本文可解決在 Azure 中使用 Resource Manager 部署模型建立之 Linux 虛擬機器的一些常見問題。如需本主題的 Windows 版本，請參閱 [Windows 虛擬機器的常見問題](virtual-machines-windows-faq.md)

## 我可以在 Azure VM 上執行什麼？

所有的訂閱者都可以在 Azure 虛擬機器上執行伺服器軟體。如需詳細資訊，請參閱 [經 Azure 背書之配送映像上的 Linux](virtual-machines-linux-endorsed-distros.md)


## 我可以使用多少的儲存體搭配虛擬機器？

每個資料磁碟最多可達 1 TB。可使用的資料磁碟數量取決於虛擬機器的大小。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

Azure 儲存體帳戶提供作業系統磁碟和任何資料磁碟的儲存空間。每個磁碟是以分頁 Blob 方式儲存的 .vhd 檔案。如需定價的詳細資料，請參閱[儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)。



## 如何存取我的虛擬機器？

您需要使用安全殼層 (SSH) 建立遠端連線來登入虛擬機器。請參閱如何[從 Windows](virtual-machines-linux-ssh-from-windows.md) 或[從 Linux 及 Mac](virtual-machines-linux-ssh-from-linux.md) 連線的指示。根據預設，SSH 允許最多 10 個並行連線。您可以編輯組態檔以增加這個數字。


如果您遇到問題，請參閱[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)。

## 我可以使用暫存磁碟 (/dev/sdb1) 儲存資料嗎？

請勿使用暫存磁碟 (/dev/sdb1) 來儲存資料。它僅用於暫時存放，您會有遺失資料且無法復原的風險。

## 我是否可以複製或再製現有的 Azure VM？

是。如需相關指示，請參閱[如何在 Resource Manager 部署模型中建立 Linux 虛擬機器的複本](virtual-machines-linux-copy-vm.md)。

## 為什麼我透過 Azure Resource Manager 沒看到加拿大中部和加拿大東部區域？

針對現有 Azure 訂用帳戶所建立的虛擬機器，不會自動註冊加拿大中部和加拿大東部這兩個新的區域。當虛擬機器透過 Azure 入口網站使用 Azure Resource Manager 部署到任何其他區域時，就會自動完成註冊。將虛擬機器部署到任何其他 Azure 區域之後，新的區域即可供後續的虛擬機器使用。

## 我可以在建立 VM 之後將 NIC 新增至此 VM 嗎？

否。新增 NIC 只能在建立期間完成。

<!---HONumber=AcomDC_0817_2016-->