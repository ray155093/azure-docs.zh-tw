---
title: "快速入門指南：在 Azure VM 上手動安裝單一執行個體 SAP HANA | Microsoft Docs"
description: "在 Azure VM 上手動安裝單一執行個體 SAP HANA 的快速入門指南"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: e95d3a57594d55b7d7176d873cde8abfa6e12ecd
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>快速入門指南：在 Azure VM 上手動安裝單一執行個體 SAP HANA
## <a name="introduction"></a>簡介
當您手動安裝 SAP NetWeaver 7.5 和 SAP HANA 1.0 SP12 時，本快速入門指南可協助您在 Azure 虛擬機器 (VM) 上設定單一執行個體的 SAP HANA 原型或示範系統。本指南不能取代 SAP 文件，但在部署 Azure 方面應多加參考。

>[!Note]
>請注意，本指南和部署 SAP HANA 到 Azure VM 相關。 有關將 SAP HANA 部署到 HANA 大型執行個體的所有考量均記載在個別文件中，這些文件可以在 https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started 找到
 

本指南假設您已熟悉如下的這類 Azure IaaS 基本知識：
 * 如何透過 Azure 入口網站或 PowerShell 部署虛擬機器或虛擬網路。
 * Azure 跨平台命令列工具 (CLI)，包括使用 JavaScript 物件通知 (JSON) 範本的選項。

本指南也假設您已熟悉 SAP HANA 和 SAP NetWeaver，以及如何在內部部署中安裝它們。

有關安裝和操作 Azure 清單上的 SAP HANA 和 SAP 應用程式執行個體，您應該要知道並熟悉更進一步的資訊，像是︰

規劃 Azure 上的 SAP 部署，包括 VNet 規劃以及 Azure 儲存體的使用，請參閱 [Azure 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。

如需部署原則和在 Azure 中部署 VM 的方法，請參閱[適用於 SAP 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。

有關 Azure 上 SAP NetWeaver ASCS、SCS、ERS 的高可用性方面的資訊，請研究 [Azure VM 上的 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)指南。

[建立 SAP NetWeaver 多 SID 組態](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)指南詳細說明如何運用 Azure 上多 SID 的 ASCS/SCS 安裝來增進效率。

在 Azure 中 Linux 驅動的 VM 上執行 SAP NetWeaver 的原則，於[在 Microsoft Azure SUSE Linux VM 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart) 中說明。 請閱讀本指南，其中詳細說明 Azure VM 中 Linux 的一些特定設定，以及如何正確地將 Azure 儲存體磁碟連結至 Linux VM 的詳細資訊。

到目前為止，SAP 僅認證 Azure VM 適用於 SAP HANA 相應增加組態。 尚未支援適合 SAP HANA 工作負載的相應放大組態。 針對相應增加組態案例中的 SAP HANA 高可用性，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)


備份 Azure VM 上 SAP HANA 資料庫的建議和可能性，這些指南中

* [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [以儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

使用 SAP 雲端應用裝置程式庫部署 S/4HANA 或 BW/4HANA，於[在 Microsoft Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) 中描述。

說明 SAP HANA 支援的作業系統的 SAP 文件是 [SAP 支援附註 #2235581 - SAP HANA︰支援的作業系統](https://launchpad.support.sap.com/#/notes/2235581/E)。 Azure VM 僅支援這些作業系統的其中一部份。 主要支援下列作業系統在 Azure 上部署 SAP HANA︰ 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

有關 SAP HANA 和不同 Linux 作業系統的其他 SAP 文件，列在︰

* [SAP 支援附註 #171356 – 在 Linux 上的 SAP 軟體︰一般資訊](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP 支援附註 #1944799 – SLES 作業系統安裝的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP 支援附註 #2205917 – 針對 SLES 12 for SAP 應用程式的 SAP HANA DB 建議作業系統設定](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP 支援附註 #1984787 – SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP 支援附註 #2009879 - 適用於 Red Hat Enterprise Linux (RHEL) 作業系統的 SAP HANA 指南](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB：適用於 RHEL 7 的建議作業系統設定](https://launchpad.support.sap.com/#/notes/2292690/E)

也請考慮這兩個有關於 Azure 中 SAP 監控的 SAP 附註︰

* 關於對 Azure 上的 Linux VM 進行 SAP「增強型監視」的 SAP Note：[SAP Note 2191498 (英文)](https://launchpad.support.sap.com/#/notes/2191498/E)。
* 具有 Linux 上 SAPOSCOL 相關資訊的 SAP Note：[SAP Note 1102124 (英文)](https://launchpad.support.sap.com/#/notes/1102124/E)。
* Microsoft Azure 上的 SAP 主要監視度量：[SAP Note 2178632 (英文)](https://launchpad.support.sap.com/#/notes/2178632/E)。


和 SAP HANA 相關的 Azure VM 類型與SAP 支援的工作負載案例，都記載於 [SAP 認證的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 經過 SAP 認證可以使用且適用於 SAP NetWeaver 或 S/4HANA 應用程式層級的 Azure VM 類型，SAP 記載在 [SAP 附註 1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E)。

>[!Note]
>只有 Azure Resource Manager (ARM) 支援 SAP-Linux-Azure，傳統部署模型 (ASM) 並不支援。 


本指南說明如何在 Azure VM 上，以下列兩種不同方式手動安裝 SAP HANA：

* 在「安裝資料庫執行個體」步驟的分散式 NetWeaver 安裝過程中使用 SAP 軟體佈建管理員 (SWPM)。
* 使用 HANA 生命週期管理員工具 hdblcm，然後安裝 NetWeaver。

您也可以使用 SWPM，並在單一 VM 中安裝所有元件 (SAP HANA、SAP 應用程式伺服器、ASCS 執行個體)，如[此處](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)所述。 雖然，本指南並未說明此選項，但必須考量的事項相同。

開始安裝之前，請務必先閱讀＜準備 Azure VM 以便手動安裝 SAP HANA＞，此內容出現在這兩個適用於 SAP HANA 安裝的檢查清單之後。 當您只使用預設的 Azure VM 設定時，這樣做有助於避免數個可能發生的基本錯誤。

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>使用 SAP SWPM 安裝 SAP HANA 的檢查清單
本節會列出當您使用 SAP SWPM 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 整份指南會以螢幕擷取畫面形式更詳細說明個別項目。

* 建立包含這兩個測試 VM 的 Azure 虛擬網路。
* 根據 Azure Resource Manager 模型，在 OS 上 (在我們的範例中是 SLES/SLES-for-SAP Applications 12 SP1) 部署兩個 Azure VM。
* 將兩個 Azure 標準或進階儲存體磁碟連結至應用程式伺服器 VM (例如 75-GB 或 500-GB 的磁碟)。
* 將進階儲存體連結至 HANA DB 伺服器 VM。 詳細資訊請參閱＜磁碟設定＞小節。
* 根據大小或輸送量需求連接多個磁碟，並在 VM內部的 OS 層級，使用邏輯磁碟區管理 (LVM) 或多個裝置的系統管理公用程式 (mdadm) 來建立等量磁碟區。
* 在連接的磁碟/邏輯磁碟區上建立 XFS 檔案系統。
* 在 OS 層級上掛接新的 XFS 檔案系統。 使用一個檔案系統來保留所有 SAP 軟體，並將另一個檔案系統用於 /sapmnt 目錄 (舉例說明) 且或許是備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 您必須執行此程序，才能防止 Linux Azure VM 上不算大的根目錄檔案系統遭到填滿。
* 在 /etc/hosts 中輸入測試 VM 的本機 IP 位址。
* 在 /etc/fstab 中輸入 nofail 參數。
* 根據與 HANA 相關的 SAP 附註以及您使用的 Linux 作業系統版本，設定 Linux 核心參數。 如需詳細資訊，請參閱＜核心參數＞一節。
* 新增交換空間。
* (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
* 從 SAP Service Marketplace 下載 SAP 軟體。
* 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
* 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
* 在 DB 伺服器 VM 上，使用 SWPM 來安裝資料庫執行個體 (包括 HANA)。
* 在應用程式伺服器 VM 上，安裝主要應用程式伺服器 (PAS)。
* 啟動 SAP 管理主控台 (MC)，並與 SAP GUI/HANA Studio (舉例說明) 連接。

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>使用 hdblcm 安裝 SAP HANA 的檢查清單
本節會基於示範或原型用途，列出當您使用 SAP hdblcm 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 整份指南會以螢幕擷取畫面形式更詳細說明個別項目。

* 建立包含這兩個測試 VM 的 Azure 虛擬網路。
* 根據 Azure Resource Manager 模型，在 OS 上 (在我們的範例中是 SLES/SLES-for-SAP Applications 12 SP1) 部署兩個 Azure VM。
* 將兩個 Azure 標準或進階儲存體磁碟連結至應用程式伺服器 VM (例如 75-GB 或 500-GB 的磁碟)。
* 將進階儲存體連結至 HANA DB 伺服器 VM。 詳細資訊請參閱＜磁碟設定＞小節。
* 根據大小或輸送量需求連接多個磁碟，並在 VM內部的 OS 層級，使用邏輯磁碟區管理 (LVM) 或多個裝置的系統管理公用程式 (mdadm) 來建立等量磁碟區。
* 在連接的磁碟/邏輯磁碟區上建立 XFS 檔案系統。
* 在 OS 層級上掛接新的 XFS 檔案系統。 使用一個檔案系統來保留所有 SAP 軟體，並將另一個檔案系統用於 /sapmnt 目錄 (舉例說明) 且或許是備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 您必須執行此程序，以協助防止 Linux Azure VM 上不算大的根目錄檔案系統遭到填滿。
* 在 /etc/hosts 中輸入測試 VM 的本機 IP 位址。
* 在 /etc/fstab 中輸入 nofail 參數。
* 根據與 HANA 相關的 SAP 附註以及您使用的 Linux 作業系統版本，設定核心參數。 如需詳細資訊，請參閱＜核心參數＞一節。
* 新增交換空間。
* (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
* 從 SAP Service Marketplace 下載 SAP 軟體。
* 在 HANA DB 伺服器 VM 上，建立群組識別碼為 1001 的群組 "sapsys"。
* 使用 HANA 資料庫生命週期管理員 (HDBLCM)，在 DB 伺服器 VM 上安裝 SAP HANA。
* 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
* 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
* 在 HANA DB 伺服器 VM 上，使用 SWPM 安裝資料庫執行個體 (包括 HANA)。
* 在應用程式伺服器 VM 上，安裝主要應用程式伺服器 (PAS)。
* 啟動 SAP MC，並透過 SAP GUI/HANA Studio 連接。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>準備 Azure VM 以便手動安裝 SAP HANA
本節包含下列主題：

* OS 更新
* 磁碟設定
* 核心參數
* 檔案系統
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>OS 更新
由於 Linux OS 經銷商會為 OS 提供更新和修正程式來協助維護安全性及確保作業順暢，因此在安裝額外的軟體之前，最好先檢查是否有可用的更新。
在很多時候，只要系統處於實際的修正層級，即可避免請求支援。 也請確定您使用︰
* 適用於 SAP 應用程式的 SUSE Linux Enterprise Server
* 適用於 SAP 應用程式或 SAP HANA 的Red Hat Enterprise Linux 

可以透過 Linux 廠商提供給您的 Linux 訂用帳戶登錄 OS 部署 (如果還沒這樣做)。 請注意，SUSE 也有適用於 SAP 應用程式的 SUSE OS 映像，已經包含服務且會自動註冊。

例如 使用 SUSE Linux，可直接以下列命令檢查可用的修補程式：

 `sudo zypper list-patches`

視缺失的種類而定，修補程式會依分類和嚴重性歸類。

常用的分類值為：security (安全性)、recommended (建議)、optional (選用)、feature (功能)、document (文件) 或 yast。

常用的嚴重性值為：critical (嚴重)、important (重要)、moderate (中)、low (低) 或 unspecified (未指定)。

zypper 只會尋找您的已安裝套件所需的更新。

因此，範例命令可能像這樣：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

如果您新增 *--dry-run* 參數，則可以測試更新，但不會實際更新系統。


### <a name="disk-setup"></a>磁碟設定
Azure 上 Linux VM 中的根目錄檔案系統大小受限。 因此，必須將額外的磁碟空間連結至 Azure VM 才能執行 SAP。 若是 SAP 應用程式伺服器 Azure VM，使用 Azure 標準儲存體磁碟的使用量可能過得去就好。 不過，若是 SAP HANA DBMS Azure VM，使用 Azure 進階儲存體磁碟用於生產與非生產實作的使用量是必要的。

根據 [SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建議使用下列的 Azure 進階儲存體組態︰ 

| VM SKU | RAM |  /hana/data 和 /hana/log <br /> 與 LVM 或 MDAADM 等量 | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建議的磁碟組態中，HANA 資料磁碟區和記錄磁碟區會放在相同的 Azure 進階儲存體磁碟中，而此種磁碟會與 LVM 或 MDADM 等量。 不需要定義任何 RAID 備援層級，因為基於備援，Azure 進階儲存體會保留磁碟的三個映像。 您可以決定不同磁碟的組態。 不過，請參閱 [SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)和 [SAP HANA Server 安裝與更新指南](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)，以確定您設定足夠的儲存空間。 也請考慮不同的 Azure 進階儲存體磁碟的不同 VHD 輸送量，如[進階儲存體磁碟限制](https://docs.microsoft.com/azure/storage/storage-premium-storage)中所述。 

依據需求，您可以為 HANA DBMS VM新增額外的進階儲存體磁碟，用於儲存資料庫或交易記錄備份。

如需這兩個用於設定等量的主要工具詳細資訊，請參閱下列文章：

* [在 Linux 上設定軟體 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [設定 Azure 中 Linux VM 的 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

針對將磁碟連結至執行 Linux 客體 OS 的 Azure VM，如需詳細資訊請參閱[如何將磁碟連結至 Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

Azure 進階儲存體可定義磁碟快取模式。 針對存放 /hana/data 和 /hana/log 的等量集，應停用磁碟快取。 針對其他磁碟區 (磁碟)，快取模式應設為唯讀。

如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../../storage/storage-premium-storage.md)。

若要尋找用於建立 VM 的範例 JSON 範本，請移至 [Azure 快速入門範本 (英文)](https://github.com/Azure/azure-quickstart-templates)。
"vm-simple-sles" 範本顯示一個基本範本，其中包含具有額外 100-GB 資料磁碟的儲存體區段。 此範本可用來當做基底。 預期的使用情況是您在您的特定組態採用此範本。

>[!Note]
>請記住務必使用 UUID 來連結 Azure 儲存體磁碟，如[在 Microsoft Azure SUSE Linux VM 上執行 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中所述。


在測試環境中，已將兩個 Azure 標準儲存體磁碟連接至 SAP 應用程式伺服器 VM，如下列螢幕擷取畫面所示。 其中一個磁碟用來儲存所有可供安裝的 SAP 軟體 (亦即 NetWeaver 7.5、SAP GUI、SAP HANA 等)。 第二個磁碟確保有足夠的可用空間可用於其他需求 (例如，備份和測試資料) 以及要在所有屬於同一個 SAP 環境的 VM 之間共用的 /sapmnt 目錄 (也就是 SAP 設定檔)。

![SAP HANA 應用程式伺服器的 [磁碟] 視窗，其中顯示兩個資料磁碟及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>核心參數
SAP HANA 需要不屬於標準 Azure 資源庫映像且必須手動設定的特定 Linux 核心設定。 依據 SUSE 或 Red Hat 的不同，參數可能不一樣。 稍早列出的 SAP 附註中提供這些參數的資訊。 在顯示的螢幕擷取畫面中，使用 SUSE Linux 12 SP1。 

SLES-for-SAP Applications 12 GA 和 SP1 具有一個可取代舊 sapconf 公用程式的新工具。 這個新工具稱為 tuned-adm，而且有特別的 SAP HANA 設定檔可供其使用。 若要調整 SAP HANA 的系統，請直接以 root 使用者身分輸入：'tuned-adm profile sap-hana'

如需有關 tuned-adm 的詳細資訊，請參閱位於下列位置的 SUSE 文章：

* [tuned-adm profile sap-hana 的相關 SLES-for-SAP Applications 12 SP1 文件 (英文)](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

在下列螢幕擷取畫面中，您可以看到 tuned-adm 如何根據必要的 SAP HANA 設定來變更 transparent_hugepage 和 numa_balancing 值。

![tuned-adm 工具會根據必要的 SAP HANA 設定來變更值](./media/hana-get-started/image005.jpg)

若要永久保留 SAP HANA 核心設定，請在 SLES 12 上使用 grub2。 如需 grub2 的詳細資訊，請移至 [SUSE 文件的＜組態檔結構＞一節 (英文)](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

下列螢幕擷取畫面顯示如何在組態檔中變更核心設定，然後使用 grub2-mkconfig 進行編譯。

![在組態檔中變更核心設定，然後使用 grub2-mkconfig 進行編譯](./media/hana-get-started/image006.jpg)

另一個選項是使用 YaST 和 [開機載入器] > [核心參數] 設定來變更這些設定。

![YaST 開機載入器中的 [核心參數] 設定索引標籤](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>檔案系統
下列螢幕擷取畫面顯示兩個檔案系統，這兩者均建立於這兩個連接之 Azure 標準儲存體磁碟上的 SAP 應用程式伺服器 VM 上。 這兩個檔案系統都屬 XFS 類型並且掛接至 /sapdata 和 /sapsoftware。

您不一定要使用這種方式來建構檔案系統。 您有其他選項可用來建構磁碟空間。 最重要的考量是防止根目錄檔案系統的可用空間用盡。

![在 SAP 應用程式伺服器 VM 上建立兩個檔案系統](./media/hana-get-started/image008.jpg)

關於 SAP HANA DB VM，一定要記住在資料庫安裝期間，當使用 SAPinst (SWPM) 和簡易「標準」安裝選項時，會在 /hana 和 /usr/sap 之下安裝所有東西。 SAP HANA 記錄備份的預設設定位於 /usr/sap 之下。 同樣地，因為必須防止根檔案系統的可用空間用盡，所以在使用 SWPM 安裝 SAP HANA 之前，請先確定 /hana 和 /usr/sap 之下有足夠的可用空間。

如需 SAP HANA 的標準檔案系統配置說明，請參閱 [SAP HANA 伺服器安裝與更新指南 (英文)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。
![在 SAP 應用程式伺服器 VM 上建立其他檔案系統](./media/hana-get-started/image009.jpg)

當您在標準 SLES/SLES-for-SAP Applications 12 Azure 資源庫映像上安裝 SAP NetWeaver 時，會顯示一則指出沒有交換空間的訊息。 若要關閉此訊息，您可以使用 dd、mkswap 和 swapon 手動新增分頁檔。 若要了解做法，請在 [SUSE 文件的＜使用 YaST Partitioner＞一節 (英文)](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) 中搜尋 "Adding a Swap File Manually" (手動新增分頁檔)。

另一個選項是使用 Linux VM 代理程式設定交換空間。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

![指出沒有足夠交換空間的快顯訊息](./media/hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
開始安裝 SAP 前另一個要牢記在心的重要事項是在 /etc/hosts 檔案中包含 SAP VM 的主機名稱和 IP 位址。 在一個 Azure 虛擬網路內部署所有的 SAP VM，然後使用內部 IP 位址。

![SAP VM 的主機名稱和 IP 位址會列於 /etc/hosts 檔案中](./media/hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

在測試階段，我們發現將 nofail 參數加入至 fstab 是個不錯的主意。 如果磁碟發生錯誤，VM 仍會啟動，而不會在開機程序中停止回應。 但請密切注意，因為在此案例中，可能無法取得額外的磁碟空間，而且程序可能會填滿根目錄檔案系統。 如果遺失 /hana，SAP HANA 將無法啟動。

![將 nofail 參數加入至 fstab](./media/hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>在 SLES 12/SLES-for-SAP Applications 12 上安裝圖形化 Gnome 桌面
本節包含下列主題：

* 在 SLES 12/SLES-for-SAP Applications 12 上安裝 Gnome 桌面和 xrdp
* 在 SLES 12/SLES-for-SAP Applications 12 上使用 Firefox 來執行 Java 型 SAP MC

您也可以使用 Xterminal 或 VNC 之類的替代方案，但截止 2016 年 9 月為止，本指南只會說明 xrdp。

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>在 SLES 12/SLES-for-SAP Applications 12 上安裝 Gnome 桌面和 xrdp
如果您具備 Microsoft Windows 背景，就可以輕鬆地在 SAP Linux VM 內直接使用圖形化桌面，來執行 Firefox、Sapinst、SAP GUI、SAP MC 或 HANA Studio，並從 Windows 電腦透過 RDP 連接到 VM。 儘管此程序可能不適合用於生產環境資料庫伺服器，但卻適用於純原型/示範環境。 以下說明如何在 Azure SLES 12/SLES-for-SAP Applications 12 VM 上安裝 Gnome 桌面：

輸入下列命令 (例如在 putty 視窗中) 來安裝 Gnome 桌面：

`zypper in -t pattern gnome-basic`

安裝 xrdp 以允許透過 RDP 連接到 VM：

`zypper in xrdp`

編輯 /etc/sysconfig/windowmanager，並將預設的視窗管理員設定為 Gnome：

`DEFAULT_WM="gnome"`

執行 chkconfig 以確保 xrdp 會在重新開機後自動啟動：

`chkconfig -level 3 xrdp on`

如果發生 RDP 連線問題，請嘗試重新啟動 (可能是從 putty 視窗)：

`/etc/xrdp/xrdp.sh restart`

如果上述的 xrdp restart 無法運作，請檢查是否有 .pid 檔案並加以移除：

`check /var/run`，然後尋找 `xrdp.pid`

請予以移除，然後再試一次重新啟動。

### <a name="sap-mc"></a>SAP MC
在安裝 Gnome 桌面之後，如果從在 Azure SLES 12/SLES-for-SAP Applications 12 VM 中執行的 Firefox 啟動圖形化 Java 型 SAP MC，可能會因為遺失 Java 瀏覽器外掛程式而顯示錯誤。

啟動 SAP MC 的 URL 為 <server>:5<instance_number>13。

如需詳細資訊，請參閱[啟動 Web 型 SAP 管理主控台 (英文)](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)。

下列螢幕擷取畫面顯示遺失 Java 瀏覽器外掛程式時所顯示的錯誤訊息。

![指出遺失 Java 瀏覽器外掛程式的錯誤訊息](./media/hana-get-started/image013.jpg)

解決這個問題的其中一種方式是只需使用 YaST 安裝遺失的外掛程式即可，如下列螢幕擷取畫面所示。

![使用 YaST 安裝遺失的外掛程式](./media/hana-get-started/image014.jpg)

重複執行 SAP 管理主控台 URL，會顯示一個對話方塊來要求您啟用外掛程式。

![要求啟用外掛程式的對話方塊](./media/hana-get-started/image015.jpg)

一個可能發生的其他問題是關於遺失檔案 (javafx.properties) 的錯誤訊息。 這與「適用於 SAP GUI 7.4 的 Oracle Java 1.8」的需求有關。 [請參閱 SAP 附註 2059429](https://launchpad.support.sap.com/#/notes/2059424)。IBM Java 版本及 SLES/SLES-for-SAP Applications 12 隨附的 openjdk 套件皆未包含所需的 javafx。 解決方案是從 Oracle 下載 Java SE8。

您可以在[適用於 openSUSE 42.1 Leap 的 SAPGui 7.4 Java (英文)](https://scn.sap.com/thread/3908306)，找到探討有關 openSUSE 與 openjdk 搭配之類似問題的文章。

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>在 NetWeaver 7.5 安裝過程中使用 SWPM 手動手動安裝 SAP HANA
本節中一系列的螢幕擷取畫面顯示當您使用 SWPM (SAPinst) 時，安裝 SAP NetWeaver 7.5 和 SAP HANA SP12 的主要步驟。 在 NetWeaver 7.5 安裝過程中，SWPM 也可將 HANA 資料庫安裝為單一執行個體。

在範例測試環境中，我們只安裝了一部進階商業應用程式程式設計 (ABAP) 應用程式伺服器。 如下列螢幕擷取畫面所示，我們使用了 [分散式系統] 選項，在一個 Azure VM 上安裝 ASCS 和主要應用程式伺服器執行個體，並在另一個 Azure VM 上安裝 SAP HANA 做為資料庫系統。

![使用 [分散式系統] 選項安裝 ASCS 和主要應用程式伺服器執行個體](./media/hana-get-started/image012.jpg)

將 ASCS 執行個體安裝於應用程式伺服器 VM 上並在 SAP 管理主控台中設定為「綠色」之後，必須將包含 SAP 設定檔目錄的 /sapmnt 目錄與 SAP HANA DB 伺服器 VM 共用。 DB 安裝步驟需要存取此資訊。 提供存取的最佳方式是使用可利用 YaST 設定的 NFS。

![顯示 ASCS 執行個體已安裝於應用程式伺服器 VM 上並設定為「綠色」的 SAP 管理主控台](./media/hana-get-started/image016.jpg)

在應用程式伺服器 VM 上，應透過 NFS 使用 **rw** 和 **no_root_squash** 選項來共用 /sapmnt 目錄。 預設值為 "ro" 和 "root_squash"，這可能導致安裝資料庫執行個體時發生問題。

![透過 NFS 使用 "rw" 和 "no_root_squash" 選項共用 /sapmnt 目錄](./media/hana-get-started/image017b.jpg)

如下列螢幕擷取畫面所示，必須在 SAP HANA DB 伺服器 VM 上，使用 **NFS 用戶端** (藉助於 YaST) 來設定應用程式伺服器 VM 中的 /sapmnt 共用。

![使用 [NFS 用戶端] 來設定 /sapmnt 共用](./media/hana-get-started/image018b.jpg)

為了執行分散式 NetWeaver 7.5 安裝，**資料庫執行個體** (如下列螢幕擷取畫面所示) 會登入 SAP HANA 資料庫伺服器 VM 並啟動 SWPM。

![登入 SAP HANA DB 伺服器 VM，然後啟動 SWPM，藉以安裝資料庫執行個體](./media/hana-get-started/image019.jpg)

選取「標準」安裝和安裝媒體的路徑之後，請輸入 DB SID、主機名稱、執行個體號碼及 DB 系統管理員密碼。

![SAP HANA 資料庫系統管理員登入頁面](./media/hana-get-started/image035b.jpg)

輸入 DBACOCKPIT 結構描述的密碼。

![DBACOCKPIT 結構描述的密碼輸入方塊](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述密碼的問題。

![輸入 SAPABAP1 結構描述密碼的問題](./media/hana-get-started/image037b.jpg)

完成工作之後，即會在 DB 安裝程序的每個階段旁邊顯示綠色核取記號。 訊息方塊視窗應該會顯示一則訊息，指出「執行 ...資料庫執行個體已完成。」

![含有確認訊息的完成工作視窗](./media/hana-get-started/image023.jpg)

安裝成功後，SAP 管理主控台也應該將 DB 執行個體顯示為「綠色」，並顯示完整的 SAP HANA 程序清單 (hdbindexserver、hdbcompileserver 等)。

![含有 SAP HANA 程序清單的 SAP 管理主控台視窗](./media/hana-get-started/image024.jpg)

下列螢幕擷取畫面顯示 SWPM 在 HANA 安裝期間於 /hana/shared 之下所建立的部分檔案結構。 因為沒有任何選項可指定不同的路徑，所以請務必在安裝 SAP HANA 之前，使用 SWPM 在 /hana 之下掛接額外的磁碟空間，以防止根目錄檔案系統的可用空間用盡。

![在 HANA 安裝期間建立 /hana/shared 檔案結構](./media/hana-get-started/image025.jpg)

這個螢幕擷取畫面顯示 /usr/sap 目錄的檔案結構。

![/usr/sap 目錄檔案結構](./media/hana-get-started/image026.jpg)

分散式 ABAP 安裝的最後一個步驟是「主要應用程式伺服器執行個體」。

![將主要應用程式伺服器執行個體顯示為最後一個步驟的 ABAP 安裝](./media/hana-get-started/image027b.jpg)

安裝主要應用程式伺服器執行個體和 SAP GUI 之後，使用 "dbacockpit" 交易來確認已正確完成 SAP HANA 安裝。

![確認安裝成功的 DBA Cockpit 視窗](./media/hana-get-started/image028b.jpg)

在最後一個步驟中，您可能想要先在 SAP 應用程式伺服器 VM 中安裝 SAP HANA Studio，然後連接至在 DB 伺服器 VM 上執行的 SAP HANA 執行個體。

![在 SAP 應用程式伺服器 VM 中安裝 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>使用 HANA 生命週期管理工具 (hdblcm) 手動安裝 SAP HANA
除了使用 SWPM 在分散式安裝過程中安裝 SAP HANA，您還能使用 hdblcm，先獨立安裝 HANA。 之後您便可安裝 SAP NetWeaver 7.5 (舉例說明)。 下列螢幕擷取畫面顯示此程序的運作方式。

以下是有關 HANA hdblcm 工具的三個資訊來源：

* [為您的工作選擇正確的 SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA 生命週期管理工具](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA 伺服器安裝與更新指南](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

為了避免 \<HANA SID\>adm 使用者 (由 hdblcm 工具建立) 的預設群組識別碼設定發生問題，在透過 hdblcm 安裝 SAP HANA 之前，請先使用群組識別碼 1001 定義名為 "sapsys" 的新群組。

![使用群組識別碼 1001 定義的新群組 "sapsys"](./media/hana-get-started/image030.jpg)

當您第一次啟動 hdblcm 時，會顯示簡易的開始功能表。 選取項目 1 [Install new system]，如下列螢幕擷取畫面所示。

![hdblcm 開始視窗中的 [Install new system] 選項](./media/hana-get-started/image031.jpg)

下列螢幕擷取畫面顯示您先前選取的所有重要選項。

> [!IMPORTANT]
> 針對 HANA 記錄和資料磁碟區命名的目錄以及安裝路徑 (此範例中的 /hana/shared) 不應為根目錄檔案系統的一部分。 目錄屬於連接至 VM 的 Azure 資料磁碟，如 Azure VM 安裝小節中所述。 這種方法將有助於避免根目錄檔案系統空間用盡的風險。 您也可以看見 HANA 系統管理員的使用者識別碼為 1005，並屬於安裝前所定義的 sapsys 群組 (識別碼為 1001)。

![先前選取的所有重要 SAP HANA 元件清單](./media/hana-get-started/image032.jpg)

您可以檢查 /etc/passwd 中的 HANA \<HANA SID\>adm (在下列螢幕擷取畫面中為 azdadm) 使用者詳細資料。

![HANA \<HANA SID\>adm 使用者詳細資料會列於 /etc/passwd 中](./media/hana-get-started/image033.jpg)

使用 hdblcm 安裝 SAP HANA 之後，您就能在 SAP HANA Studio 中看見檔案結構。 尚未提供 SAPABAP1 結構描述 (其中包括所有的 SAP NetWeaver 資料表)。

![SAP HANA Studio 中的 SAP HANA 檔案結構](./media/hana-get-started/image034.jpg)

安裝 SAP HANA 之後，就能在其上安裝 SAP NetWeaver。 如此螢幕擷取畫面所示，已使用 SWPM 來將安裝當成「分散式安裝」執行 (如上一節所述)。 當您使用 SWPM 安裝資料庫執行個體時，可以使用 hdblcm 輸入相同的資料 (例如，主機名稱、HANA SID 和執行個體號碼)。 SWPM 接著會使用現有的 HANA 安裝，並加入多個結構描述。

![使用 SWPM 執行分散式安裝](./media/hana-get-started/image035b.jpg)

下列螢幕擷取畫面顯示 SWPM 安裝步驟，而您可以在其中輸入 DBACOCKPIT 結構描述的相關資料。

![輸入 DBACOCKPIT 結構描述資料的 SWPM 安裝步驟](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述的相關資料。

![輸入 SAPABAP1 結構描述的相關資料](./media/hana-get-started/image037b.jpg)

完成 SWPM 資料庫執行個體安裝後，就能在 SAP HANA Studio 中看見 SAPABAP1 結構描述。

![SAP HANA Studio 中的 SAPABAP1 結構描述](./media/hana-get-started/image038b.jpg)

最後，在完成 SAP 應用程式伺服器和 SAP GUI 安裝後，您就能使用 "dbacockpit" 交易來驗證 HANA DB 執行個體。

![使用 "dbacockpit" 交易驗證 HANA DB 執行個體](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 軟體下載
您可以從 SAP Service Marketplace 下載軟體，如下列螢幕擷取畫面所示。

* 下載適用於 Linux/HANA 的 NetWeaver 7.5：

 ![用於下載 NetWeaver 7.5 的 SAP 服務安裝和升級視窗](./media/hana-get-started/image001.jpg)
* 下載 HANA SP12 平台版本：

 ![用於下載 HANA SP12 平台版本的 SAP 服務安裝和升級視窗](./media/hana-get-started/image002.jpg)


