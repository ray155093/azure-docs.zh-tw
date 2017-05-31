---
title: "在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA | Microsoft Docs"
description: "如何在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 89356670737ed7b9adc5df51466ce71eb73b3cd4
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何安裝和設定 Azure 上的 SAP HANA (大型執行個體)

安裝 SAP HANA 是由您負責，而您可以在遞交新的 SAP HANA on Azure (大型執行個體) 伺服器之後，並在 Azure VNet 與「HANA 大型執行個體」單位之間已建立連線之後，啟動活動。 請注意，依據 SAP 原則，執行 SAP HANA 安裝的人員必須是已驗證而能執行 SAP HANA 安裝的人員 (也就是已通過「SAP 技術認證協會」(Certified SAP Technology Associate) 之「SAP HANA 安裝認證測驗」的人員)，或是 SAP 認證的系統整合者 (SI)。

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>收到 HANA 大型執行個體單位之後的前幾個步驟

在收到「HANA 大型執行個體」並建立對執行個體的存取和連線之後，**第一步**是向您的 OS 提供者註冊執行個體的 OS。 這包括在「HANA 大型執行個體」單位可連線的 Azure VM 中，於已部署之 SUSE SMT 的執行個體註冊 SUSE Linux OS。 或是必須向您必須連接的 Red Hat Subscription Manager 註冊 RedHat OS。 另請參閱這份[文件](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的備註。 此步驟也是持續修補 OS 所需的步驟。 這是一項屬於客戶職責的工作。 有關 SUSE，請在[這裡](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)尋找安裝和設定 SMT 的文件。

**第二步**是檢查是否有特定 OS 版次/版本的新修補程式和修正。 請檢查「HANA 大型執行個體」的修補程式等級是否為最新狀態。 根據 OS 修補程式/版次和 Microsoft 可部署對映像所做之變更的時機，可能會有未包含最新修補程式的情況。 因此，在接管「HANA 大型執行個體」單位並向 Linux 散發者註冊 OS 安裝之後，就必須檢查特定 Linux 廠商是否已在這期間發行與安全性、功能、可用性及效能相關的修補程式而需要套用。

**第三步**是查看相關的「SAP 附註」，以在特定的 OS 版次/版本上安裝及設定 SAP HANA。 因為依據個別的安裝情況，建議事項會有所變更或是對「SAP 附註」或組態所做的變更會有所不同，所以 Microsoft 將無法總是完美地設定「HANA 大型執行個體」單位。 因此，身為客戶，您必須閱讀「SAP 附註」(下面列出最基本的附註)、檢查必要 OS 版次/版本的組態、套用組態設定 (如果尚未這麼做)。

具體而言，請檢查下列參數並最後調整成：

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

從 SLES12 SP1 和 RHEL 7.2 開始，必須在 /etc/sysctl.d 目錄的組態檔中設定這些參數。 例如，必須建立名為 91-NetApp-HANA.conf 的組態檔。 如果是舊版 SLES 和 RHEL，則必須 /etc/sysctl.conf 中設定這些參數。

針對所有 RHEL 版次以及從 SLES12 開始的版本， 
- sunrpc.tcp_slot_table_entries = 128

參數必須在 /etc/modprobe.d/sunrpc-local.conf 中設定。 如果該檔案不存在，則必須先透過新增下列項目來建立它： 
- options sunrpc tcp_max_slot_table_entries=128

**第四步**是檢查您「HANA 大型執行個體單位」的系統時間。 系統將會使用代表「HANA 大型執行個體戳記」所在 Azure 區域位置的系統時區來部署執行個體。 您可以自由變更您所擁有執行個體的系統時間或時區。 如果您這麼做並為您的租用戶訂購更多執行個體，請做好準備，您將必須調整新交付之執行個體的時區。 在移交執行個體之後，Microsoft 作業即無法洞察您為執行個體設定的系統時區。 因此，為新部署的執行個體設定的時區可能不會與您變更後的時區相同。 如此一來，身為客戶，您就必須負責檢查並視需要調整已移交之執行個體的時區。 

**第五步**是檢查 etc/hosts。 在移交刀鋒伺服器時，會針對不同的用途為它們指派不同的 IP 位址 (請參閱下一節)。 請檢查 etc/hosts。 如果將單位新增到現有的租用戶，請勿期望系統會使用先前交付之系統的 IP 位址來正確維護新部署之系統的 etc/hosts。 因此，必須倚賴身為客戶的您來檢查設定是否正確，如此新部署的執行個體才能進行互動並解析您租用戶中先前所部署單位的名稱。 

## <a name="networking"></a>網路
我們假設您已依照設計 Azure VNet 並將這些 VNet 連接到「HANA 大型執行個體」的建議操作，如下列文件所述：

- [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上 SAP HANA (大型執行個體) 的基礎結構和連接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

關於單一單位的網路功能，有一些值得一提的細節。 每個「HANA 大型執行個體」單位都隨附兩個或三個 IP 位址，這些位址會指派給該單位的兩個或三個 NIC 連接埠。 在 HANA 向外延展組態與「HANA 系統複寫」案例中會使用三個 IP 位址。 其中一個指派給單位之 NIC 的 IP 位址是來自 [Azure 上 SAP HANA (大型執行個體) 的概觀和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的伺服器 IP 集區。

在獲指派兩個 IP 位址的情況下，單位的分配應該會看起來如下：

- 指派給 eth0.xx 的 IP 位址應該來自您向 Microsoft 提交的「伺服器 IP 集區」位址範圍。 此 IP 位址將用來在 OS 的 /etc/hosts 中進行維護。
- 指派給 eth1.xx 的 IP 位址應該用於對 NFS 的通訊。 因此，「不」需要在 etc/hosts 中維護這些位址，即可允許租用戶內的執行個體對執行個體流量。

就「HANA 系統複寫」或 HANA 向外延展部署案例而言，並不適合使用有兩個指派之 IP 位址的刀鋒伺服器組態。 如果指派兩個 IP 位址只是為了部署這類組態，請連絡「Microsoft 服務管理」在第三個 VLAN 中指派第三個 IP 位址。 針對有三個 IP 位址指派在三個 NIC 連接埠上的「HANA 大型執行個體」單位，適用下列情況：

- 指派給 eth0.xx 的 IP 位址應該來自您向 Microsoft 提交的「伺服器 IP 集區」位址範圍。 因此，此 IP 位址將不用來在 OS 的 /etc/hosts 中進行維護。
- 指派給 eth1.xx 的 IP 位址應該用於對 NFS 儲存體的通訊。 因此，不應該在 etc/hosts 中維護此類型的位址。
- eth2.xx 應該專門用來在 etc/hosts 中維護，以供不同執行個體之間的通訊使用。 這些也會是需要在向外延展 HANA 組態中維護的 IP 位址，以作為 HANA 用於節點間組態的 IP 位址。



## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由 SAP HANA on Azure Service Management 透過 SAP 建議的最佳做法進行設定，請參閱 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)技術白皮書。 隨著您閱讀這份文件及了解「HANA 大型執行個體」，您將發現這些單位隨附相當大的 HANA/data 磁碟區，並且還有一個 HANA/log/backup 磁碟區。 我們之所以將 HANA/data 的大小設定成如此大，是因為我們為身為客戶的您提供的儲存體快照就是使用這麼大的磁碟區。 這意謂著您執行的儲存體快照越多，就需要越多供這些快照使用的空間。 HANA/log/backup 磁碟區不是用來放置資料庫備份的磁碟區，而是可用來存放 HANA 交易記錄的備份磁碟區。 在未來的儲存體快照自助服務版本中，我們將會以提高快照建立頻率作為此特定磁碟區的目標，並且如果您想要加入「HANA 大型執行個體」基礎結構所提供的災害復原功能，還可將複寫到災害復原站台的頻率也提高。請參閱 [Azure 上 SAP Hana (大型執行個體) 的高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的詳細資料 

除了所提供的儲存體之外，您還可以購買額外的儲存體容量 (增量單位為 1 TB)。 此額外儲存體可以做為新的磁碟區新增至 HANA 大型執行個體。

在與「Microsoft 服務管理」進行上線訓練時，客戶會為 <SID>ADM 使用者和 sapsys 群組指定「使用者識別碼」(UID) 和「群組識別碼」(GID) (例如：1000,500)。安裝 SAP HANA 系統時，請務必使用這些相同的值。

儲存體控制器和大型執行個體戳記中的節點會同步處理至 NTP 伺服器。 當您針對 NTP 伺服器同步處理 SAP HANA on Azure (大型執行個體) 單位和 Azure VM 時，Azure 或大型執行個體戳記中的基礎結構與計算單位之間應該不會耗費太多時間。

為了將 SAP HANA 到底層所使用的儲存體都最佳化，您應該一併設定下列 SAP HANA 組態參數：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
針對從 SAP HANA 1.0 到 SPS12 的版本，您可以在安裝 SAP HANA 資料庫時設定這些參數，如 [SAP 附註 #2267798 - SAP HANA 資料庫組態 (英文)](https://launchpad.support.sap.com/#/notes/2267798) 所述

您也可以透過使用 hdbparam 架構，在安裝 SAP HANA 資料庫之後設定這些參數。 

在 SAP HANA 2.0，hdbparam 架構已被取代。 因此，必須使用 SQL 命令來設定參數。 如需詳細資料，請參閱 [SAP 附註 #2399079：在 HANA 2 中已刪除 hdbparam (英文)](https://launchpad.support.sap.com/#/notes/2399079)。


## <a name="operating-system"></a>作業系統

[SUSE Linux Enterprise Server 12 SP1 for SAP 應用程式](https://www.suse.com/products/sles-for-sap/hana)是針對 SAP HANA on Azure (大型執行個體) 安裝的 Linux 的分佈。 這個特定的分佈提供&quot;現成&quot; SAP 特有功能，(包括預先設定的參數，有效地在 SLES 上執行 SAP)。

請參閱 SUSE 網站上的[資源程式庫/技術白皮書](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)和 SAP Community Network (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)，以取得與在 SLES 上部署 SAP HANA 相關的有用資源 (包括高可用性的設定、SAP 作業特定的安全性強化等等)。

額外和有用的 SLES 相關連結︰

- [SUSE Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 的最佳做法：加入佇列複寫 – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (包括 SLES 12 for SAP 應用程式)

SAP 支援附註適用於實作 SLES 12 SP1 上的 SAP HANA︰

- [SAP 支援附註 #1944799 – SLES 作業系統安裝的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP 支援附註 #2205917 – 針對 SLES 12 for SAP 應用程式的 SAP HANA DB 建議作業系統設定](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP 支援附註 #1984787 – SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #171356 – 在 Linux 上的 SAP 軟體︰一般資訊](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>時間同步處理

SAP 對於組成 SAP 系統的各種元件的時間差異非常敏感。 具有錯誤標題 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 簡短傾印可能會非常熟悉，如果您已使用 SAP (基本) 很長一段時間，因為這些簡短傾印會在不同伺服器或 VM 的系統時間離開太遠時顯示。

針對 SAP HANA on Azure (大型執行個體)，在 Azure 中完成的時間同步處理不適用於大型執行個體戳記中的計算單位。 這並不適用於在 Azure 中原生執行 SAP 應用程式 (在 VM 上)，因為 Azure 可確保正確同步處理系統時間。 如此一來，必須設定不同時間伺服器，可供 Azure VM 上執行的 SAP 應用程式伺服器與 HANA 大型執行個體上執行的 SAP HANA 資料庫執行個體使用。 大型執行個體戳記中的儲存體基礎結構與 NTP 伺服器進行時間同步處理。



