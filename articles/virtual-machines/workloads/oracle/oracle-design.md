---
title: "在 Azure 上設計和實作 Oracle 資料庫 | Microsoft Docs"
description: "在 Azure 環境中設計和實作 Oracle 資料庫。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: zh-tw
ms.lasthandoff: 07/10/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>在 Azure 上設計和實作 Oracle 資料庫

## <a name="assumptions"></a>假設

- 規劃從內部部署移轉至 Azure 的 Oracle 資料庫
- 了解 Oracle AWR 報表的各種計量
- 具有應用程式效能和平台使用量的基準

## <a name="goals"></a>目標

- 了解最佳化 Azure 上的 Oracle 部署
- 探索 Azure 環境中 Oracle 資料庫的效能調整選項

### <a name="on-premises-vs-on-azure"></a>內部部署與在 Azure 上

以下是將內部部署應用程式移轉至 Azure 時的一些重要考量。 與內部部署不同，Azure 中的資源 (VM、磁碟、VNet 等) 是在其他用戶端之間共用。 此外，可以根據需求來節流資源。 Azure 更積極讓失敗存活 (MTTR)，而不是聚焦於避免失敗 (MTBF)。

下表列出一些差異。

> 
> |  | **內部部署** | **Azure** |
> | --- | --- | --- |
> | **網路功能** |LAN/WAN  |SDN - 軟體定義網路|
> | **安全性群組** |IP/連接埠限制工具 |[網路安全性群組 (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **恢復功能** |MTBF (平均失敗時間) |MTTR (平均復原時間)|
> | **預定的維修** |修補/升級|[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (Azure 所管理的修補/升級) |
> | **Resource** |專用  |與其他用戶端共用|
> | **區域** |資料中心 |[區域配對](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **儲存體** |SAN/實體磁碟 |[Azure 受管理儲存體](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **調整** |垂直調整 |水平調整|


### <a name="requirements"></a>需求

- 決定資料庫大小和成長率
- 決定 IOPS 需求，可以根據 Oracle AWR 報表或其他網路監視工具進行評估。

## <a name="configuration-options"></a>設定選項

有四個可能的區域可以進行調整，以改善 Azure 環境中的效能。

- 虛擬機器大小
- 網路輸送量
- 磁碟類型和設定
- 磁碟快取設定

### <a name="generate-awr-report"></a>產生 AWR 報表

如果您有現有 Oracle 資料庫，並規劃移轉至 Azure。 您可以執行 Oracle AWR 報表取得計量 (IOPS、Mbps GiB 等)，然後根據收集到的計量選擇 VM。 或者，連絡基礎結構小組，取得類似的資訊。

您可以考慮在一般和尖峰工作負載期間執行 AWR 報表，以比較差異。 根據這些報表，您可以根據平均工作負載或最大工作負載來調整 VM 大小。

以下是如何產生 AWR 報表的範例。

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>重要計量

以下是可從 AWR 報表取得的計量。

- 核心總數
- CPU 時脈速度
- 記憶體總計 (GB)
- CPU 使用率
- 尖峰資料傳送速率
- IO 變更率 (讀/寫)
- 重做記錄速率 (MBPs)
- 網路輸送量
- 網路延遲速率 (低/高)
- 資料庫大小 (GB)
- 透過 SQL*Net 從/至用戶端收到的位元組

### <a name="virtual-machine-size"></a>虛擬機器大小

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1.VM 大小的初始預估是根據 AWR 報表中的 CPU/記憶體/IO 使用量。

您可以查看的一個事項是前五個定時前景事件，其指出系統瓶頸位置。

例如：在下圖中，記錄檔案同步在頂端，這是等候 LGWR 將記錄緩衝區寫入重做記錄檔的次數。 這指出需要較佳效能儲存體/磁碟。 此外，本圖也會顯示 CPU (核心) 數目和記憶體數量。

![AWR 報表頁面的螢幕擷取畫面](./media/oracle-design/cpu_memory_info.png)

下圖顯示總讀取和寫入 IO。 在報表時間期間，有 59 GB 的讀取和 247.3 GB 的寫入。

![AWR 報表頁面的螢幕擷取畫面](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2.預估 VM 大小

根據您從 AWR 報表收集到的資訊，下一個步驟是選擇符合您需求且大小類似的 VM。 可用的 VM 清單會列在下列連結中：[虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory)。

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3.根據 ACU 微調類似 VM 系列的 VM 大小

在您選擇 VM 之後，請注意 VM 的 ACU。 您可以根據可能較適合您需求的 ACU 值，選擇類似的 VM。 如需詳細資訊，請參閱 [Azure 計算單位 (ACU)](https://docs.microsoft.com/azure/virtual-machines/windows/acu)。

![ACU 單位頁面的螢幕擷取畫面](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>網路輸送量

輸送量與 IOPS 之間的關聯如下所示。

![輸送量的螢幕擷取畫面](./media/oracle-design/throughput.png)

總網路輸送量是根據下列項目進行預估：
- SQL*Net 流量
- MBps x 伺服器數目 (輸出串流，例如 Data Guard)
- 其他項目，例如應用程式複寫

![SQL*Net 輸送量的螢幕擷取畫面](./media/oracle-design/sqlnet_info.png)

根據您的網路頻寬需求，有各種閘道類型可供您選擇，例如 Basic、VpnGw、ExpressRoute。 您可以從 [VPN 閘道定價頁面](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h)取得詳細資訊。

建議

- 與內部部署相較之下，網路延遲較高。 減少網路來回行程可以大幅改善效能。
- 合併相同虛擬機器上具有高交易或 “Chatty” 應用程式的應用程式，以減少來回行程。

### <a name="disk-types-and-configurations"></a>磁碟類型和設定

- 預設 OS 磁碟：具有持續性資料和快取。 此磁碟最適合在啟動時間進行 OS 存取，但不是針對交易式或資料倉儲 (分析) 工作負載所設計。

- 未受管理磁碟：您可以管理用來儲存虛擬硬碟 (VHD) 檔案 (對應至您的 VM 磁碟) 的儲存體帳戶。 VHD 檔案會以分頁 Blob 的形式儲存在 Azure 儲存體帳戶中。

- Managed Disks 受控磁碟：Azure 會管理您用於 VM 磁碟的儲存體帳戶。 您可以指定需要的磁碟類型 (進階或標準) 和磁碟大小。 Azure 會為您建立並管理該磁碟。

- 進階儲存體磁碟 (最適用於生產工作負載)：「進階儲存體」支援可附加至特定大小系列 VM (例如 DS、DSv2、GS 和 F 系列) 的 VM 磁碟。 進階磁碟會隨附不同的大小，而且您可以選擇 32 GB 到 4096 GB 的各種磁碟大小。 每個磁碟大小都有自己的效能規格。 端視您的應用程式需求而定，您可以將一或多個磁碟連結至您的 VM。

當您從入口網站建立新的受管理磁碟時，可以選擇您想要使用之指定磁碟類型的 [帳戶類型]。 請務必知道，並非所有可用的磁碟都會顯示在下拉式方塊中。 原因是 Azure 儲存體與 VM 整合，而且限制跨 SKU (例如，磁碟機數目上限、最大 IOP)。 因此，當您選取特定大小的 VM 時，只會根據該 VM 大小來顯示可用的進階儲存體 SKU。

![受管理磁碟頁面的螢幕擷取畫面](./media/oracle-design/premium_disk01.png)

如需詳細資訊，請參閱 [Azure 進階儲存體](https://docs.microsoft.com/azure/storage/storage-premium-storage)。

在 VM 上設定儲存體之後，您可能想要在建立資料庫之前對磁碟進行負載測試。 請注意，考量延遲和輸送量的 IO 速率可以協助您判斷 VM 是否支援具有延遲目標的預期輸送量。

有數種工具可以進行應用程式負載測試，例如 Oracle Orion、Sysbench、Fio 等。

部署 Oracle 資料庫之後重新執行負載測試、啟動一般和尖峰工作負載，結果會顯示您環境的基準線。

較重要的可能是根據 IOPS 速率來調整儲存體大小，而非儲存體大小。 例如，如果必要 IOPS 是 5000，但您只需要 200 GB。 您可能仍然會取得 P30 類別進階磁碟，即使隨附 200-GB 以上的儲存體大小也是一樣。

IOPS 速率可以取自 AWR 報表，它是透過重做記錄、實體讀取和寫入速率所決定。

![AWR 報表頁面的螢幕擷取畫面](./media/oracle-design/awr_report.png)

例如：重做大小是每秒 12200000 個位元組，相當於 11.63 MBPs。IOPS 是 12200000/2358 = 5174。

清楚了解 IO 需求之後，即可選擇最符合這些需求的磁碟機組合。

建議

- 針對資料的資料表空間，使用受管理儲存體或 Oracle ASM，將 I/O 工作負載分散到一些磁碟。
- 隨著 I/O 區塊大小的增加，針對讀取和寫入密集作業，新增更多資料磁碟。
- 增加大型循序處理序的區塊大小
- 使用資料壓縮來減少 IO (適用於資料和索引)
- 區隔不同資料磁碟上的重做記錄、系統、暫時和復原 TS
- 不建議將任何應用程式檔案放在預設 OS 磁碟 (/dev/sda)。 此磁碟最適合用於快速 VM 開機時間，可能不會提供您應用程式的良好效能。

### <a name="disk-cache-settings"></a>磁碟快取設定

有三個主機快取選項。

- 唯讀：快取所有要求，以供未來讀取。 直接持續保存到 Windows Azure 儲存體 Blob 的所有寫入

- 讀寫：這是「預先讀取」演算法。 快取讀取和寫入，以供未來讀取。 非直接寫入式寫入已先持續保存到本機快取。 針對 SQL Server，因為它使用直接寫入式，所以寫入會持續保存到 WA 儲存體。它提供最低磁碟延遲以用於輕量工作負載。

- 無 (停用)：這可讓您略過快取。 所有資料都會傳輸至磁碟，並持續保存到 Windows Azure 儲存體。 這種方法可提供您最高 I/O 速率來進行 I/O 密集式工作負載。 您也需要考量「交易成本」。

建議

若要最大化輸送量，建議使用 [無] 開始進行主機快取。 針對進階儲存體，一定要注意，您必須在使用 [唯讀] 或 [無] 選項掛接檔案系統時停用「屏障」。 將具有 UUID 的 /etc/fstab 檔案更新到磁碟。

如需詳細資訊，請參閱[適用於 Linux VM 的進階儲存體](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms)。

![受管理磁碟頁面的螢幕擷取畫面](./media/oracle-design/premium_disk02.png)

- 針對 OS 磁碟，使用預設 [讀取/寫入] 快取。
- 針對 SYSTEM、TEMP 和 UNDO，使用 [無] 進行快取。
- 針對 DATA，使用 [無] 進行快取，但是，如果您的資料庫是唯讀或讀取密集，請使用 [唯讀] 快取。

除非您卸載 OS 層級的磁碟機，然後在變更後重新予以掛接，否則儲存資料磁碟設定之後，就無法變更主機 (AFAIK) 快取設定。


## <a name="security"></a>安全性

在您安裝並設定 Azure 環境之後。 下一步是保護網路安全。 以下是一些建議：

- NSG 原則

可以定義 NSG 的子網路或 NIC。  它更容易控制應用程式防火牆這類事項之安全性和強制路由的子網路層級存取。

- Jumpbox ![Jumpbox 拓撲頁面的螢幕擷取畫面](./media/oracle-design/jumpbox.png)

基於更安全的存取，系統管理員不應該直接連接至應用程式服務或資料庫。 Jumpbox 作為系統管理員機器與 Azure 資源之間的媒體。

系統管理員機器應該是僅限 Jumpbox 的 IP 受限存取。 Jumpbox 接著可以存取應用程式/資料庫。

- 私人網路 (子網路)

建議將應用程式服務和資料庫放在不同的子網路上。 因此，NSG 原則可以設定更佳的控制。


## <a name="additional-readings"></a>其他文章：

- [設定 Oracle ASM](configure-oracle-asm.md)
- [設定 Oracle Data Guard](configure-oracle-dataguard.md)
- [設定 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 備份和復原](oracle-backup-recovery.md)

## <a name="next-steps"></a>後續步驟

[教學課程︰建立高可用性 VM](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 Azure CLI 範例](../../linux/cli-samples.md)

