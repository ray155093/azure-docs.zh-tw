---
title: 有關 Windows VM 的磁碟和 VHD | Microsoft Docs
description: 了解 Azure 中 Windows 虛擬機器的磁碟和 VHD 的基本知識。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>有關 Azure 虛擬機器的磁碟和 VHD
就像任何其他電腦，Azure 中的虛擬機器會使用磁碟做為儲存作業系統、應用程式和資料的位置。 所有 Azure 虛擬機器都至少有兩個磁碟 - 一個 Windows 作業系統磁碟和一個暫存磁碟。 作業系統磁碟是從映像建立，且作業系統磁碟與該映像都是儲存在 Azure 儲存體帳戶中的虛擬硬碟 (VHD)。 虛擬機器也可以有一或多個資料磁碟，而這些磁碟也會儲存成 VHD。 本文也適用於 [Linux 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="operating-system-disk"></a>作業系統磁碟
每個虛擬機器都有一個連接的作業系統磁碟。 它是註冊為 SATA 磁碟機，並預設標示為 C: 磁碟機。 此磁碟的最大容量為 1023 GB。 

## <a name="temporary-disk"></a>暫存磁碟
系統會自動為您建立暫存磁碟。 暫存磁碟預設會標示為 D: 磁碟機，並用於儲存 pagefile.sys。 

暫存磁碟的大小會依據虛擬機器的大小而改變。 如需詳細資訊，請參閱 [Windows 虛擬機器的大小](virtual-machines-windows-sizes.md)。

> [!WARNING]
> 請勿在暫存磁碟上儲存資料。 它提供應用程式和處理程序暫時的儲存空間，且其用意僅為儲存分頁檔等資料。 若要將此磁碟重新對應至不同的磁碟機代號，請參閱 [變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md)。
> 
> 

如需有關 Azure 如何使用暫存磁碟的詳細資訊，請參閱 [Understanding the temporary drive on Microsoft Azure Virtual Machines (了解 Microsoft Azure 虛擬機器上的暫存磁碟機)](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>資料磁碟
資料磁碟是連接至虛擬機器的 VHD，用來儲存應用程式資料或其他您需要保留的資料。 資料磁碟註冊為 SCSI 磁碟機，並以您選擇的字母標示。  每個資料磁碟的最大容量為 1023 GB。 虛擬機器的大小會決定您可以連接之磁碟的數量，以及您可以用來裝載磁碟的儲存體類型。

> [!NOTE]
> 如需有關虛擬機器容量的詳細資訊，請參閱 [Windows 虛擬機器的大小](virtual-machines-windows-sizes.md)。
> 
> 

當您從映像建立虛擬機器時，Azure 會建立作業系統磁碟。 如果您使用包含資料磁碟映像時，Azure 建立虛擬機器時也會建立資料磁碟。 若沒有，則您可以建立虛擬機器後再新增資料磁碟。

您可以隨時將資料磁碟「連接」  到虛擬機器來將該磁碟新增到虛擬機器中。 您可以使用您已上傳或複製到儲存體帳戶的 VHD，或 Azure 提供的 VHD。 連接資料磁碟時，會透過在 VHD 上加上「租約」將 VHD 檔案與 VM 關聯，如此一來，當它仍處於連接狀態時，就無法將它從儲存體中刪除。

## <a name="about-vhds"></a>關於 VHD
Azure 中使用的 VHD 是以分頁 Blob 儲存在 Azure 標準或進階儲存體帳戶中的 .vhd 檔案。 如需有關分頁 Blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](https://msdn.microsoft.com/library/ee691964.aspx)。 如需有關進階儲存體的詳細資訊，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。

Azure 支援 VHD 格式的固定磁碟。 固定格式會以線性方式展示檔案內的邏輯磁碟，因此磁碟位移 X 會儲存於 Blob 位移 X。Blob 結尾的一個小頁尾會說明 VHD 的屬性。 由於大多數磁碟內都有相當大的未用範圍，因此固定格式通常會浪費空間。 不過，Azure 會以疏鬆格式來儲存 .vhd 檔案，因此您可同時享有固定和動態磁碟的好處。 如需詳細資訊，請參閱[開始使用虛擬硬碟](https://technet.microsoft.com/library/dd979539.aspx)。

Azure 中所有您想要做為來源以建立磁碟或映像的 .vhd 檔案，均為唯讀。 Azure 會在您建立磁碟或映像製作 .vhd 檔案的複本。 取決於您使用 VHD 的方式，這些複本可為唯讀或讀取及寫入。

當您從映像建立虛擬機器時，Azure 會以來源 .vhd 檔案複本為虛擬機器建立磁碟。 若要防止意外刪除，Azure 會在任何用來建立映像、作業系統磁碟或資料磁碟的來源 .vhd 檔案上加上租用。

您必須先刪除磁碟或映像來移除租約，才能刪除來源 .vhd 檔案。 若要將虛擬機器、作業系統磁碟及來源 .vhd 檔案一次全部刪除，只要刪除虛擬機器並刪除所有關聯的磁碟即可。 不過，刪除作為資料磁碟來源的 .vhd 檔案檔案需要依設定的順序執行幾個步驟。 首先，您需將磁碟與虛擬機器中斷連結，接著刪除磁碟，然後再刪除 .vhd 檔案。

> [!WARNING]
> 如果您刪除儲存體中的來源 .vhd 檔案從或刪除您的儲存體帳戶，Microsoft 便無法為您復原該資料。
> 
> 

## <a name="next-steps"></a>後續步驟
* [連接磁碟](virtual-machines-windows-attach-disk-portal.md) 來為您的 VM 新增額外的儲存空間。
* [將 Windows VM 映像上傳至 Azure](virtual-machines-windows-upload-image.md) ，以在建立新的 VM 時使用。
* [變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-classic-change-drive-letter.md) ，讓您的應用程式可以使用 D: 磁碟機來儲存資料。

<!--HONumber=Oct16_HO2-->


