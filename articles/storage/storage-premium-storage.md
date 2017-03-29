---
title: "高效能進階儲存體和 Azure VM 磁碟 | Microsoft Docs"
description: "討論高效能進階儲存體及非受控和受控 Azure VM 磁碟。 Azure DS 系列、DSv2 系列和 GS 系列 VM 支援進階儲存體。"
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 26e78f559fa9a82183a26034580148e39331a214
ms.lasthandoff: 03/17/2017


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>高效能進階儲存體及非受控和受控 Azure VM 磁碟
針對執行時需要大量 I/O 之工作負載的虛擬機器 (VM)，Microsoft Azure 進階儲存體可提供高效能、低延遲的磁碟支援。 使用「進階儲存體」的 VM 磁碟會將資料儲存在固態硬碟 (SSD) 上。 您可以將應用程式的 VM 磁碟移轉到「Azure 進階儲存體」，以利用這些磁碟的速度和效能。

Azure VM 支援附加數個進階儲存體磁碟，讓您應用程式的每一 VM 最多可擁有 64 TB 的儲存體。 使用「進階儲存體」時，應用程式的每一 VM 可達到每秒 80,000 個輸入/輸出作業 (IOPS)，而每一 VM 的磁碟輸送量可達到 2000 MB/秒，且讀取作業的延遲極低。

Azure 使用進階儲存體，提供您將要求較高的企業應用程式 (例如 Dynamics AX、Dynamics CRM、Exchange Server、SharePoint Farms 與 SAP Business Suite) 隨即轉移至雲端的能力。 您可以在進階儲存體上執行強調效能、需要持續高效能和低延遲的資料庫工作負載，例如 SQL Server、Oracle、MongoDB、MySQL、Redis。

> [!NOTE]
> 建議您將任何需要高 IOPS 的虛擬機器磁碟移轉到「進階儲存體」，以發揮應用程式最佳效能。 如果您的磁碟不需要高 IOPS，您可以在「標準儲存體」中維護它來限制成本，這會將虛擬機器磁碟資料儲存在「硬碟機 (HDD)」上而非 SSD 上。
> 

有兩種方式可以為 Azure VM 建立「進階」磁碟︰

**非受控磁碟**︰這是原始方法，由您管理儲存對應至 VM 磁碟的 VHD 檔案時所用的儲存體帳戶。 VHD 檔案會以分頁 Blob 的形式儲存在儲存體帳戶中。 

**[Azure 受控磁碟](storage-managed-disks-overview.md)**︰這項功能會為您管理用於 VM 磁碟的儲存體帳戶。 您只需要指定類型 (進階或標準)，還有您需要的磁碟大小，Azure 就會替您建立並管理磁碟。 您不必擔心將磁碟分配至多個儲存體帳戶，以確保維持在儲存體帳戶的延展性限制內 - Azure 會為您處理。

即使這兩種磁碟都可用，我們建議您使用受控磁碟，以利用它的許多功能。

若要開始使用 Azure 進階儲存體，請瀏覽[免費開始使用](https://azure.microsoft.com/pricing/free-trial/)。 

如需將現有的 VM 移轉至進階儲存體，請參閱[將現有的 Azure Windows VM 移轉至受控磁碟](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md)或[將現有的 Azure Linux VM 移轉至受控磁碟](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md)。

> [!NOTE]
> 目前大部分地區都支援進階儲存體。 您可以在[依地區的 Azure 服務](https://azure.microsoft.com/regions/#services)中查看支援大小系列 VM (DS、DSV2、Fs 和 GS) 的區域，以找出可用的區域清單。
> 

## <a name="premium-storage-features"></a>進階儲存體功能

讓我們看看進階儲存體的一些功能。

**進階儲存體磁碟**：「Azure 進階儲存體」支援可附加至特定大小系列 VM (包括 DS、DSv2、GS 和 Fs 系列) 的 VM 磁碟。 您有三種磁碟大小可選擇 -- P10 (128GiB)、P20 (512GiB) 及 P30 (1024GiB) -- 每種都有自己的效能規格。 端視您的應用程式需求而定，您可以將一或多個這類磁碟附加至您的 VM。 在下一節的 [進階儲存體延展性和效能目標 ](#premium-storage-scalability-and-performance-targets) 中，我們會更詳細地說明規格。

**進階儲存體 Blob**：進階儲存體支援分頁 Blob，用於儲存適用於 VM 的永續性非受控磁碟。 不同於標準儲存體，進階儲存體不支援區塊 Blob、附加 Blob、檔案、資料表或佇列。
放在進階儲存體帳戶中的任何物件都會是分頁 Blob，而且符合其中一種支援的佈建大小。 這就是進階儲存體帳戶不是用於儲存小型 blob 的原因。

**進階儲存體帳戶**︰若要開始使用進階儲存體，請為非受控磁碟建立進階儲存體帳戶。 如果您偏好使用 [Azure 入口網站](https://portal.azure.com)，您可以指定「進階」效能層級和「本地備援儲存體 (LRS)」作為複寫選項，以建立進階儲存體帳戶。 使用[儲存體 REST API](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) 2014-02-14 版或更新版本並將類型指定為 “Premium_LRS”、[服務管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 2014-10-01 版或更新版本 (傳統部署)、[Azure 儲存體資源提供者 REST API 參考](/rest/api/storagerp) (Resource Manager 部署) 以及 [Azure PowerShell](../powershell-install-configure.md) 0.8.10 版或更新版本，也可以建立進階儲存體帳戶。 在下一節的 [進階儲存體延展性和效能目標](#premium-storage-scalability-and-performance-targets.md)中，深入了解進階儲存體帳戶限制。

**進階本地備援儲存體**：進階儲存體帳戶僅支援本地備援儲存體 (LRS) 作為複寫選項，這表示它在單一區域內會保留三份資料。 針對區域性災害復原，您必須使用 [Azure 備份服務](../backup/backup-introduction-to-azure-backup.md)來備份位於不同區域的 VM 磁碟，並以 GRS 儲存體帳戶作為備份保存庫。 

Azure 使用儲存體帳戶作為非受控磁碟的容器。 當您建立具有非受控磁碟的 Azure DS、DSv2、GS 或 Fs VM 並選取進階儲存體帳戶時，您的作業系統和資料磁碟會儲存在該儲存體帳戶中。

## <a name="premium-storage-supported-vms"></a>進階儲存體支援的 VM
進階儲存體支援 DS 系列、DSv2 系列、GS 系列和 Fs 系列 VM。 這些 VM 可同時搭配標準和進階儲存體磁碟使用。 您不能將進階儲存體磁碟與非「進階儲存體」相容的 VM 系列搭配使用。

如需可用 Azure VM 類型和 Windows VM 大小的資訊，請參閱 [Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如需可用 VM 類型和 Linux VM 大小的資訊，請參閱 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

以下是 DS、DSv2、GS 及 Fs 系列 VM 的一些功能：

**雲端服務**：可以將 DS 系列 VM 加入至僅包含 DS 系列 VM 的雲端服務。 請勿將 DS 系列 VM 加入至包含非 DS 系列 VM 的現有雲端服務。 您可以將現有的 VHD 移轉到僅執行 DS 系列 VM 的新雲端服務。 如果您想要為裝載 DS 系列 VM 的新雲端服務保留相同的虛擬 IP 位址 (VIP)，請使用 [保留的 IP 位址](../virtual-network/virtual-networks-instance-level-public-ip.md)。 GS 系列 VM 可以新增至僅執行 G 系列 VM 的現有雲端服務。

**作業系統磁碟**︰可在進階儲存體上執行的 VM 可以設定為使用進階或標準作業系統 (OS) 磁碟。 我們建議使用進階儲存體型的 OS 磁碟，以獲得最佳體驗。

**資料磁碟**：在進階儲存體上執行的相同 VM 中，您可以同時使用進階和標準磁碟。 使用進階儲存體，您可以佈建 VM 並將幾個永續性資料磁碟附加至 VM。 如有需要，您可以跨磁碟等量磁碟區以增加磁碟區的容量和效能。

> [!NOTE]
> 如果您使用[儲存空間](http://technet.microsoft.com/library/hh831739.aspx)等量分割進階儲存體磁碟，應該設定為每個使用的磁碟各一個磁柱。 否則，等量磁碟區的整體效能可能會因為磁碟流量分配不平均而比預期的效能還低。 根據預設，伺服器管理員使用者介面 (UI) 可讓您設定最多 8 個磁碟的磁柱。 如果您有 8 個以上的磁碟，則必須使用 PowerShell 建立磁碟區，並且手動指定磁柱數目。 否則，即使您擁有更多磁碟，伺服器管理員 UI 還是會繼續使用 8 個資料行。 例如，如果您在單一等量磁碟區組有 32 個磁碟，您應該指定 32 個資料行。 您可以使用 *New-VirtualDisk* PowerShell Cmdlet 的 [NumberOfColumns](http://technet.microsoft.com/library/hh848643.aspx) 參數，指定虛擬磁碟所使用的資料行數目。 如需詳細資訊，請參閱[儲存體空間概觀](http://technet.microsoft.com/library/hh831739.aspx)和[儲存體空間常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)。
> 

**快取**：支援進階儲存體的大小系列 VM 具有獨特的快取功能，可讓您享有超越基礎進階儲存體磁碟效能的高層級輸送量和延遲表現。 您可以在進階儲存體磁碟上將磁碟快取原則設定為 [唯讀]、[讀寫] 或 [無]。 所有進階資料磁碟的預設磁碟快取原則都是 [唯讀]，而作業系統磁碟的磁碟快取原則則是 [讀寫]。 使用正確的組態設定，以達到應用程式的最佳效能。 例如，對於讀取繁重或唯讀資料磁碟 (如 SQL Server 資料檔)，將磁碟快取原則設定為 [唯讀]。 例如，對於寫入繁重或唯寫資料磁碟 (如 SQL Server 記錄檔)，將磁碟快取原則設定為 [無]。 在 [使用進階儲存體設計高效能](storage-premium-storage-performance.md)中，深入了解如何最佳化進階儲存體。

**分析**︰若要分析使用進階儲存體磁碟的 VM 效能，您可以在 [Azure 入口網站](https://portal.azure.com)中啟用 VM 診斷。 如需詳細資料，請參閱[使用 Azure 診斷擴充功能監視 Microsoft Azure 虛擬機器](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) 。 若要查看磁碟效能，請使用作業系統工具，例如適用於 Windows VM 的 [Windows 效能監視器](https://technet.microsoft.com/library/cc749249.aspx)和適用於 Linux VM 的 [IOSTAT](http://linux.die.net/man/1/iostat)。

**VM 規模限制和效能**︰在 IOPS、頻寬及每個 VM 可附加的磁碟數目上，進階儲存體支援的每個 VM 大小都有規模限制和效能規格。 使用進階儲存體磁碟搭配 VM 時，請確定 VM 上有足夠的 IOPS 和頻寬可用來運送磁碟流量。
例如，STANDARD_DS1 VM 有 32 MB/秒的專用頻寬可運送進階儲存體磁碟流量。 P10 進階儲存體磁碟可以提供 100 MB/秒的頻寬。 如果 P10 進階儲存體磁碟附加至此 VM，則最高只能達到 32 MB/秒，而無法達到 P10 磁碟可提供的 100 MB/秒。

目前在 DS 系列上最大的 VM 是 Standard_DS15_v2，它在所有磁碟最多可以提供 960 MB/秒。 GS 系列上最大的 VM 是 Standard_GS5，它在所有磁碟最多可以提供 2000 MB/秒。
請注意，這些限制僅適用於磁碟流量，不包含快取命中數和網路流量。 VM 網路流量有不同的頻寬，與進階儲存體磁碟專用的頻寬不同。

如需有關「進階儲存體」支援之 VM 的最新 IOPS 和輸送量 (頻寬) 上限資訊，請參閱 [Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

若要了解進階儲存體磁碟及其 IOPS 與輸送量限制，請參閱本文的[進階儲存體延展性和效能目標](#premium-storage-scalability-and-performance-targets)一節中的表格。

## <a name="premium-storage-scalability-and-performance-targets"></a>進階儲存體延展性和效能目標
在這一節中，我們將說明在使用進階儲存體時必須考慮的延展性和效能目標。

進階儲存體帳戶有下列延展性目標：

| 總帳戶容量 | 本地備援儲存體帳戶總頻寬 |
| --- | --- | 
| 磁碟容量：35 TB <br>快照容量：10 TB | 每秒最多 50 Gbps (輸入 + 輸出) |

* 輸入是指傳送至某個儲存體帳戶的所有資料 (要求)。
* 輸出是指從某個儲存體帳戶接收的所有資料 (回應)。

如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](storage-scalability-targets.md)。

如果您針對非受控磁碟使用進階儲存體帳戶，而您的應用程式超過單一儲存體帳戶的延展性目標，請考慮移轉至受控磁碟。 如果您不想要移轉至受控磁碟，請將應用程式建置為使用多個儲存體帳戶，並將資料分散到那些儲存體帳戶。 例如，如果要將 51 TB 的磁碟附加到多個 VM，請將它們分散到兩個儲存體帳戶，因為單一進階儲存體帳戶的限制是 35 TB。 請務必確認單一進階儲存體帳戶的佈建磁碟決不超過 35 TB。

### <a name="premium-storage-disk-limits"></a>進階儲存體磁碟限制
當您佈建進階儲存體磁碟時，磁碟的大小決定 IOPS 和輸送量 (頻寬) 上限。 有三種類型的進階儲存體磁碟：P10、P20 及 P30。 每種各有特定的 IOPS 和輸送量限制，如下表所示：

|進階儲存體磁碟類型 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| 磁碟大小 | 128 GB | 512 GB | 1024 GB (1 TB) |
| 每一磁碟的 IOPS | 500 | 2300 | 5000 |
每一磁碟的輸送量 | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 |

> [!NOTE]
> 請確定您的 VM 上有足夠的頻寬可用來運送磁碟流量，如本文稍早[進階儲存體支援的 VM](#premium-storage-supported-vms) 一節所述。 否則，您的磁碟輸送量和 IOPS 將會根據 VM 限制而不是上表所述的磁碟限制而受限於較低的值。  
> 
> 

以下是有關進階儲存體延展性和效能目標，您必須知道的一些重要事項︰

* **佈建的容量和效能**︰當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P30 磁碟，Azure 會為該磁碟佈建 1024 GB 儲存體容量、5000 IOPS 和 200 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。

* **磁碟大小**：Azure 會將磁碟大小對應 (無條件進位) 至表格中指定之最接近的進階儲存體磁碟選項。 例如，大小為 100 GiB 的磁碟會分類為 P10 選項，最多可執行 500 IOPS，輸送量可達 100 MB/秒。 同樣地，大小為 400 GiB 的磁碟會分類為 P20 選項，最多可執行 2300 IOPS，輸送量可達 150 MB/秒。
  
> [!NOTE]
> 您可以輕易增加現有磁碟的大小。 例如，您可以將 30 GB 磁碟的大小增加到 128 GB，甚至 1 TB。 或者，因為您需要更多容量或更多的 IOPS 和輸送量，您也可以將 P20 磁碟轉換為 P30 磁碟。 
> 
 
* **IO 大小**：輸入/輸出 (I/O) 單位大小為 256 KB。 如果要傳送的資料少於 256 KB，會視為單一 I/O 單位。 較大的 I/O 大小則會視為大小是 256 KB 的多個 I/O。 例如，1100 KB 的 I/O 會視為五個 I/O 單位。

* **輸送量**：輸送量限制包含寫入至磁碟，以及從磁碟而不是從快取的讀取。 例如，P10 磁碟具有每一磁碟 100 MB/秒的輸送量。 P10 磁碟的有效輸送量範例如下：

| 每個 P10 磁碟的輸送量上限 | 從磁碟的非快取讀取 | 對磁碟的非快取寫入 |
| --- | --- | --- |
| 每秒 100 MB | 每秒 100 MB | 0 |
| 每秒 100 MB | 0 | 每秒 100 MB |
| 每秒 100 MB | 每秒 60 MB | 每秒 40 MB |

* **快取命中數**：快取命中數不受限於磁碟配置的 IOPS 或輸送量。 例如，當您在進階儲存體支援的 VM 上使用具有「唯讀」快取設定的資料磁碟時，從快取提供的「讀取數」並不受限於磁碟的 IOPS 和輸送量上限。 因此，如果工作負載以讀取為主，您可以從磁碟獲得極高的輸送量。 請注意，在 VM 層級上，根據 VM 大小，快取會受到不同的 IOPS 和輸送量限制。 DS 系列 VM 大約有 4000 IOPS，快取與本機 SSD IO 是每個核心 33 MB/秒。 GS 系列 VM 的限制為 5000 IOPS，而快取與本機 SSD IO 是每個核心 50 MB/秒。 

## <a name="throttling"></a>節流
如果您的應用程式 IOPS 或輸送量超過進階儲存體磁碟的配置限制，或如果您在 VM 上所有磁碟的磁碟總流量超過 VM 可用的磁碟頻寬限制，您可能會看到節流。 若要避免節流，建議您根據佈建之磁碟的延展性和效能目標，以及根據 VM 可用的磁碟頻寬，來限制磁碟的擱置 I/O 要求數。  

當您的應用程式設計為避免節流情況時，可以達到最低延遲。 但是，如果磁碟的擱置 I/O 要求數過小，您的應用程式便無法達到磁碟最大的 IOPS 和輸送量層級。

下列範例示範如何計算節流層級。 所有計算都是以 256 KB 的 I/O 單位大小為基礎：

### <a name="example-1"></a>範例 1：
在 P10 磁碟上，您的應用程式一秒內有 495 個 16 KB 大小的 I/O 單位。 這些會被視為每秒 495 個 I/O 單位 (IOPS)。 如果您在該秒內嘗試 2 MB 的 I/O，I/O 單位的總數會等於 495 + 8。 這是因為當 I/O 單位大小是 256 KB 時，2 MB 的 I/O 會產生 2048 KB / 256 KB = 8 個 I/O 單位。 因為 495 + 8 的總和超出磁碟 500 個 IOPS 的限制，因此會發生節流情況。

### <a name="example-2"></a>範例 2：
在 P10 磁碟上，您的應用程式有 400 個 256 KB 大小的 I/O 單位。 耗用的總頻寬是 (400 * 256) / 1024 = 100 MB/秒。 P10 磁碟的輸送量限制為每秒 100 MB。 如果您的應用程式嘗試在該秒內執行更多 I/O 便會發生節流情況，因為會超出配置的限制。

### <a name="example-3"></a>範例 3：
DS4 VM 連接了兩個 P30 磁碟。 每個 P30 磁碟有每秒 200 MB 的輸送量。 不過，DS4 VM 有每秒 256 MB 的磁碟總頻寬容量。 因此，此 DS4 VM 上連接的磁碟無法同時達到最大的輸送量。 若要解決這個問題，您可以在一個磁碟上維持每秒 200 MB 的流量，在另一個磁碟上維持每秒 56 MB 的流量。 如果您的磁碟流量總和超過每秒 256 MB，磁碟流量就會發生節流。

> [!NOTE]
> 如果磁碟流量大多包含小型 I/O，您的應用程式在達到輸送量限制前很可能會先達到 IOPS 限制。 但是，如果磁碟流量大多包含大型 I/O，您的應用程式可能會達到輸送量限制，而非 IOPS 限制。 您可以使用最佳的 I/O 大小和限制磁碟的擱置 I/O 要求數，以最大化應用程式 IOPS 和輸送量容量。
> 

若要了解如何使用進階儲存體設計高效能，請參閱 [使用進階儲存體設計高效能](storage-premium-storage-performance.md)一文。

## <a name="snapshots-and-copy-blob"></a>快照集和複製 Blob

對於儲存體服務而言，VHD 檔案是分頁 Blob。 您可以建立分頁 Blob 的快照集，然後複製到另一個位置，例如不同的儲存體帳戶。

### <a name="unmanaged-disks"></a>非受控磁碟

就像對標準儲存體使用快照集一樣，您可以使用相同方式為非受控進階磁碟建立[增量快照集](storage-incremental-snapshots.md)。 因為進階儲存體僅支援本地備援儲存體 (LRS) 作為複寫選項，所以建議您建立快照集，然後將那些快照集複製到異地備援標準儲存體帳戶。 如需詳細資訊，請參閱 [Azure 儲存體備援選項](storage-redundancy.md)。

如果磁碟已附加至 VM，則磁碟上不允許某些 API 作業。 例如，只要磁碟連接至 VM，您就無法在該 Blob 上執行 [複製 Blob](/rest/api/storageservices/fileservices/Copy-Blob) 作業。 您必須先使用[快照 Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) REST API 方法建立該 Blob 的快照，然後對該快照執行[複製 Blob](/rest/api/storageservices/fileservices/Copy-Blob) 以複製連接的磁碟。 或者，您可以中斷連結磁碟，再執行任何必要的作業。

進階儲存體 blob 快照集有下列限制︰

| 進階儲存體限制 | 值 |
| --- | --- |
| 最大 每個 Blob 的快照數目 | 100 |
| 快照的儲存體帳戶容量 (只包含快照中的資料，不包含基底 Blob 中的資料) | 10 TB |
| 最小 連續快照之間的時間 | 10 分鐘 |

若要維護快照集的異地備援副本，您可以使用 AzCopy 或「複製 Blob」，將進階儲存體帳戶中的快照複製到異地備援的標準儲存體帳戶。 如需詳細資訊，請參閱[使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)和[複製 Blob](/rest/api/storageservices/fileservices/Copy-Blob)。

如需對進階儲存體帳戶中的分頁 Blob 執行 REST 作業的詳細資訊，請參閱[在 Azure 儲存體帳戶使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969)。

### <a name="managed-disks"></a>受控磁碟

受控磁碟的快照集是受控磁碟的唯讀複本，儲存為標準受控磁碟。 受控磁碟目前不支援[增量快照集](storage-incremental-snapshots.md)，但未來會支援。 若要了解如何建立受控磁碟的快照集，請參閱[在 Windows 中使用受管理的快照集建立 VHD 的複本並儲存為 Azure 受控磁碟](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)或[在 Linux 中使用受管理的快照集建立 VHD 的複本並儲存為 Azure 受控磁碟](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)

如果受控磁碟已附加至 VM，則磁碟上不允許某些 API 作業。 例如，當磁碟已附加至 VM 時，您無法產生共用存取簽章 (SAS) 來執行複製作業。 請先建立磁碟的快照集，再複製快照集。 或者，您可以中斷連結磁碟，然後產生共用存取簽章 (SAS) 來執行複製作業。


## <a name="using-linux-vms-with-premium-storage"></a>在 Premium 儲存體使用 Linux VM
請參閱下方重要指示，以了解如何在進階儲存體上設定 Linux VM：

* 對於快取設定為「唯讀」或「無」的所有進階儲存體磁碟，您在掛接檔案系統時必須停用「阻礙」，才能達到進階儲存體體的延展性目標。 此案例中您不需要阻礙，因為對這些快取設定而言，寫入進階儲存體磁碟是持久的。 寫入要求成功完成時，資料就已寫入永久性的存放區。 請選擇您的檔案系統，然後使用下列方法停用「阻礙」：
  
* 如果您使用 **reiserFS**，請使用掛接選項 "barrier=none" 停用阻礙 (若要啟用阻礙，請使用 "barrier=flush")
* 如果您使用 **ext3/ext4**，請使用掛接選項 "barrier=0" 停用阻礙 (若要啟用阻礙，請使用 "barrier=1")
* 如果您使用 **XFS**，請使用掛接選項 "nobarrier" 停用阻礙 (若要啟用阻礙，請使用 "barrier" 選項)
* 對於快取設定為「讀寫」的進階儲存體磁碟，應該啟用阻礙以持久寫入。
* 對於要在 VM 重新開機後保存的磁碟機標籤，您必須以參考磁碟的 UUID 更新 /etc/fstab。 也請參閱[將受控磁碟新增至 Linux VM](../virtual-machines/virtual-machines-linux-add-disk.md)。

以下是我們驗證能使用 Premium 儲存體的 Linux 散發套件。 我們建議您升級 VM 到至少其中一個版本 (或更新版本)，以便獲得 Premium 儲存體較佳的效能和穩定性。 此外，部分版本需要最新的適用於 Microsoft Azure 的 Linux Integration Services 4.0 版。 請依照下面提供的連結進行下載及安裝。 當我們完成其他驗證後，將繼續在清單中新增更多映像。 請注意，我們的驗證顯示效能依映像而有所不同，而且也取決於工作負載特性和映像上的設定。 不同的映像已針對不同種類的工作負載進行調整。

| 配送映像 | 版本 | 支援的核心 | 詳細資料 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [LIS4 (必要)](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *請參閱下方注意事項* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 (建議使用) ](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *請參閱下方注意事項* |
| RHEL | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 或 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Openlogic CentOS 的 LIS 驅動程式

執行 OpenLogic CentOS VM 的客戶應該執行下列命令以安裝最新的驅動程式：

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

您需要重新開機才能啟動驅動程式。

## <a name="pricing-and-billing"></a>價格和計費

使用 Premium 儲存體時，需考量下列計費資訊：

* 進階儲存體磁碟/blob 大小
* 進階儲存體快照集
* 輸出資料傳輸

**進階儲存體磁碟/blob 大小**：進階儲存體磁碟/blob 的計費根據磁碟/blob 的佈建大小而定。 Azure 會將佈建大小對應 (無條件進位) 至[使用進階儲存體時的延展性和效能目標](#premium-storage-scalability-and-performance-targets)一節的表格中最接近的進階儲存體磁碟選項。 每個磁碟會對應至其中一個支援的佈建大小，並據此計費。 任何已佈建的磁碟都是按每月的 Premium 儲存體優惠價格以每小時的方式計費。 例如，如果您在佈建完 P10 磁碟的 20 小時後刪除它，則會以 20 小時計算 P10 解決方案的費用。 這與寫入磁碟的實際資料量或使用的 IOPS/輸送量無關。

**進階非受控磁碟快照集**：進階非受控磁碟上的快照集會因為使用的額外容量而產生費用。 如需有關快照的資訊，請參閱 [建立 Blob 的快照](/rest/api/storageservices/fileservices/Snapshot-Blob)。

**進階受控磁碟快照集**：受控磁碟的快照集是磁碟的唯讀複本，儲存為標準受控磁碟。 受控快照集的成本與標準受控磁碟相同。 例如，如果您建立 128 GB 進階受控磁碟的快照集，快照集的成本就等於 128 GB 標準受控磁碟的成本。  

**輸出資料傳輸**： [輸出資料傳輸](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

如需進階儲存體、進階儲存體支援的 VM 和受控磁碟的價格詳細資訊，請參閱：

* [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)
* [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [受控磁碟價格](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Azure 備份服務支援 

針對區域性災害復原，您必須使用 [Azure 備份服務](../backup/backup-introduction-to-azure-backup.md)來備份位於不同區域的 VM 磁碟，並以 GRS 儲存體帳戶作為備份保存庫。

同時對未受管理的磁碟和受控磁碟使用 Azure 備份服務，利用以時間為基礎的備份、簡易 VM 還原和備份保留原則，來建立備份作業。 您可以在[針對具有受控磁碟的 VM 使用 Azure 備份服務](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)與[針對具有未受管理的磁碟之 VM 使用 Azure 備份服務](../backup/backup-azure-vms-first-look-arm.md)深入了解 

## <a name="next-steps"></a>後續步驟
如需 Azure 進階儲存體的詳細資訊，請參閱下列文章。

### <a name="design-and-implement-with-azure-premium-storage"></a>使用 Azure 進階儲存體進行設計和實作
* [使用進階儲存體設計高效能](storage-premium-storage-performance.md)
* [在 Azure 儲存體帳戶使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>作業指引
* [移轉到 Azure 進階儲存體](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>部落格文章
* [Azure 進階儲存體正式推出](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [宣佈 GS 系列︰將進階儲存體支援加入至公用雲端中的最大 VM](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

