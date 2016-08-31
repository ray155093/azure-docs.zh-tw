<properties
	pageTitle="Windows VM 的常見問題集 | Microsoft Azure"
	description="針對以 Resource Manager 模型建立的 Windows 虛擬機器，提供一些相關常見問題的解答。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Windows 虛擬機器的常見問題 


本文可解決在 Azure 中使用 Resource Manager 部署模型建立之 Windows 虛擬機器的一些常見問題。如需本主題的 Linux 版本，請參閱 [Linux 虛擬機器的常見問題](virtual-machines-linux-faq.md)

## 我可以在 Azure VM 上執行什麼？

所有的訂閱者都可以在 Azure 虛擬機器上執行伺服器軟體。如需在 Azure 中執行 Microsoft 伺服器軟體的支援原則資訊，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/kb/2721672)。

特定版本的 Windows 7 和 Windows 8.1 可供 MSDN Azure 權益訂閱者和 MSDN 開發與測試隨用隨付訂閱者 (針對開發與測試工作) 使用。如需詳細資訊 (包括指示和限制)，請參閱 [MSDN 訂閱者的 Windows 用戶端映像](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。


## 我可以使用多少的儲存體搭配虛擬機器？

每個資料磁碟最多可達 1 TB。可使用的資料磁碟數量取決於虛擬機器的大小。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。

Azure 儲存體帳戶提供作業系統磁碟和任何資料磁碟的儲存空間。每個磁碟是以分頁 Blob 方式儲存的 .vhd 檔案。如需定價的詳細資料，請參閱[儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)。


## 如何存取我的虛擬機器？

您必須使用遠端桌面連線 (RDP) 為 Windows VM 建立遠端連線。如需指示，請參閱[如何連接和登入執行 Windows 的 Azure 虛擬機器](virtual-machines-windows-connect-logon.md)。除非將伺服器設定為遠端桌面服務工作階段主機，否則最多支援兩個並行連線。


如果您在使用「遠端桌面」時遇到問題，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)。

如果您熟悉 Hyper-V，您可能正在尋找類似 VMConnect 的工具。Azure 沒有提供類似的工具，因為並不支援主控台存取虛擬機器。

## 我可以使用暫存磁碟 (預設為 D: 磁碟機) 儲存資料嗎？

您不應使用暫存磁碟儲存資料。暫存磁碟僅提供暫存空間，因此您會有遺失資料且無法復原的風險。當虛擬機器移動到不同的主機時就可能發生這種情況。可能要移動虛擬機器的一些原因是調整虛擬機器的大小、更新主機，或主機上的硬體故障等等。

如果應用程式需要使用 D: 磁碟機代號，您可以重新指派磁碟機代號，讓暫存磁碟使用 D: 以外的代號。如需相關指示，請參閱[變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md)。

## 如何變更暫存磁碟的磁碟機代號？

您可以透過移動分頁檔並重新指派磁碟機代號來變更磁碟機代號，但必須確定會以特定的順序執行這些步驟。如需相關指示，請參閱[變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md)。

## 我是否可以將現有的 VM 新增到可用性設定組？

不可以。如果您希望 VM 屬於可用性設定組的一部分，就必須在設定組當中建立 VM。目前不支援在建立 VM 之後，再將其加入可用性設定組。

## 我是否可以將虛擬機器上傳到 Azure？

是。如需指示，請參閱[將 Windows VM 映像上傳至 Azure](virtual-machines-windows-upload-image.md)。

## 我是否可以調整 OS 磁碟機的大小？

是。如需指示，請參閱[如何擴充 Azure 資源群組之虛擬機器的 OS 磁碟機](virtual-machines-windows-expand-os-disk.md)。

## 我是否可以複製或再製現有的 Azure VM？

是。如需指示，請參閱[如何在 Resource Manager 部署模型中建立 Windows 虛擬機器的複本](virtual-machines-windows-specialized-image.md)。

## 為什麼我在 Azure Resource Manager 中沒看到加拿大中部和加拿大東部區域？

針對現有 Azure 訂用帳戶所建立的虛擬機器，不會自動註冊加拿大中部和加拿大東部這兩個新的區域。當虛擬機器透過 Azure 入口網站使用 Azure Resource Manager 部署到任何其他區域時，就會自動完成註冊。將虛擬機器部署到任何其他 Azure 區域之後，新的區域即可供後續的虛擬機器使用。

## Azure 是否支援 Linux VM？

是。若要快速建立 Linux VM 以試用，請參閱[使用入口網站在 Azure 上建立 Linux VM](virtual-machines-linux-quick-create-portal.md)。

## 我可以在建立 VM 之後將 NIC 新增至此 VM 嗎？

否。新增 NIC 只能在建立期間完成。

<!---HONumber=AcomDC_0817_2016-->