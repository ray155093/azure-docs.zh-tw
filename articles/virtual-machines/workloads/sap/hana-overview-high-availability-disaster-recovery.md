---
title: "SAP HANA on Azure (大型執行個體) 的高可用性和災害復原 | Microsoft Docs"
description: "建立高可用性並為 SAP HANA on Azure (大型執行個體) 的災害復原做規劃。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
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
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f95e944fc3ec3a831d97386443eb644420ae54dc
ms.lasthandoff: 04/03/2017


---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP Hana (大型執行個體) 在 Azure 上的高可用性和災害復原 

高可用性和災害復原對執行具任務關鍵性的 SAP HANA on Azure (大型執行個體) 伺服器來說，是相當重要的層面。 請務必與 SAP、您的系統整合者或 Microsoft 合作，以便正確建構及實作正確的高可用性/災害復原策略。 也務必考量您環境特有的復原點目標和復原時間目標。

## <a name="high-availability"></a>高可用性

Microsoft 支援「現成」的 SAP HANA 高可用性方法，其中包括：

- **儲存體複寫：**儲存體系統能夠將所有資料複寫到另一個位置 (在相同資料中心之內或之外)。 SAP HANA 獨立運作，不依賴此方法。
- **HANA 系統複寫︰**將 SAP HANA 中的所有資料複寫到個別的 SAP HANA 系統。 復原時間目標是透過定期的資料複寫而最小化。 SAP HANA 支援非同步、記憶體內同步及同步模式 (僅針對位於相同資料中心內或距離 100 公里內的 SAP HANA 系統建議使用)。 以 HANA 大型執行個體戳記目前的設計而言，HANA 系統複寫只可用於高可用性。
- **主機自動容錯移轉︰**一種本機錯誤復原解決方案，作為系統複寫的替代選項。 當主要節點變成無法使用時，有一或多個待命 SAP HANA 節點會以相應放大模式設定，而 SAP HANA 會自動容錯移轉到另一個節點。

如需 SAP HANA 高可用性的詳細資訊，請參閱下列 SAP 資訊：

- [SAP HANA 高可用性白皮書](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP Academy 的 SAP HANA 系統複寫影片](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支援附註 #1999880 - SAP HANA 系統複寫常見問題集](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 支援附註 #2165547 - SAP HANA 系統複寫環境內的 SAP HANA 備份與還原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支援附註 #1984882 - 使用 SAP HANA 系統複寫以最短/零停機時間的方式進行硬體交換](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>災害復原

在一個地緣政治區域的兩個 Azure 區域中提供 SAP HANA on Azure (大型執行個體)。 兩個不同區域的兩個大型執行個體戳記之間有直接網路連線，可在災害復原期間複寫資料。 資料的複寫是以儲存體基礎結構為基礎。 依預設不會複寫。 已訂購災害復原的客戶設定才會複寫。 在目前的設計中，HANA 系統複寫無法用於災害復原。

不過，若要利用災害復原，您必須開始設計連到兩個不同 Azure 區域的網路連線。 在做法上，您需要一條從主要 Azure 區域中的內部部署連接的 ExpressRoute 線路，還需要另一條從內部部署連接到災害復原區域的線路。 此措施可解決整個 Azure 區域 (包含 Microsoft Enterprise Edge (MSEE) 路由器位置) 發生問題的情況。

還有第二種措施，您可以將連接到其中一個區域中 SAP HANA on Azure (大型執行個體) 的所有 Azure 虛擬網路，都連接到這兩個 ExpressRoute 線路。 此措施可解決將內部部署位置與 Azure 連接的 MSEE 位置之中，只有一個停止運作的情況。

下圖顯示災害復原的最佳組態：

![災害復原的最佳組態](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

網路災害復原組態的最佳案例是要有兩條從內部部署連接到兩個不同 Azure 區域的 ExpressRoute 線路。 一條線路通往區域 #1，執行生產環境執行個體。 第二條 ExpressRoute 線路連至區域 #2，執行一些非生產的 HANA 執行個體。 (如果整個 Azure 區域 (包含 MSEE 和大型執行個體戳記) 脫離網路，這就很重要)。

還有第二種措施，不同的虛擬網路連接到不同的 ExpressRoute 線路，而這些線路又連接到 SAP HANA on Azure (大型執行個體)。 您可以略過 MSEE 失敗的位置，或降低災害復原的復原點目標 (稍後討論)。

災害復原設定的後續需求包括：

- 您必須訂購與生產 SKU 大小相同的 SAP HANA on Azure (大型執行個體) SKU，並部署在災害復原區域中。 這些執行個體可用來執行測試、沙箱或 QA HANA 執行個體。
- 針對您在災害復原網站中想要復原的每個 SAP HANA on Azure (大型執行個體) SKU，必要的話，您必須訂購災害復原設定檔。 這個動作會導致配置存放磁碟區，這些磁碟區是從您生產區域將儲存體複寫到災害復原區域時的目標。

符合上述需求之後，您必須自行啟動儲存體複寫。 在用於 SAP HANA on Azure (大型執行個體) 的儲存體基礎結構中，儲存體複寫的基礎是儲存體快照集。 若要啟動災害復原複寫，您需要執行：

- 開機 LUN 的快照集 (如先前所述)。
- HANA 相關磁碟區的快照集 (如先前所述)。

執行這些快照集之後，在與災害復原區域中的災害復原設定檔相關聯的磁碟區上，就會植入這些磁碟區的初始複本。

接著，每小時會使用最新的儲存體快照集，以複寫存放磁碟區上出現的差異。

這項設定可達成的復原點目標從 60 到 90 分鐘。 若要在災害復原案例中達成更好的復原點目標，請從 SAP HANA on Azure (大型執行個體) 將 HANA 交易記錄備份複製到其他 Azure 區域。 為了達成此復原點目標，請執行下列作業︰

1. 儘可能經常將 HANA 交易記錄複製到 /hana/log/backup。
2. 在交易記錄備份完成後，將這些備份複製到 Azure 虛擬機器 (VM)，此 VM 位於連接到 SAP HANA on Azure (大型執行個體) 伺服器的虛擬網路中。
3. 從該 VM 上，將備份複製到災害復原區域中位於虛擬網路上的 VM。
4. 將交易記錄備份保留在該區域的 VM 中。

當發生災害時，在災害復原設定檔部署到實際伺服器之後，請將交易記錄備份從 VM 複製到現在是災害復原區域中主要伺服器的 SAP HANA on Azure (大型執行個體)，然後還原這些備份。 此復原可行，因為災害復原磁碟上的 HANA 狀態就是 HANA 快照集的狀態。 這是可進一步還原交易記錄備份的位移點。

## <a name="backup-and-restore"></a>備份與還原

操作資料庫的其中一個最重要層面，就是確保可以保護資料庫免於各種災難事件的影響。 這些災害可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫時，若提供還原到任何時間點 (例如在某人刪除重要資料之前) 的功能，便可允許還原到儘可能接近發生中斷之前的狀態。

必須執行兩種類型的備份，才能獲得最佳結果：

- 資料庫備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過以儲存體快照執行備份的方式來進行更徹底的備份。 執行記錄備份對還原資料庫 (以及從已經認可的交易清空記錄) 來說也很重要。

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- 自己動手做 (DIY)。 在計算以確保磁碟空間足夠之後，請使用磁碟備份方法來執行完整資料庫和記錄備份 (備份到這些磁碟)。 在一段時間後，備份會複製到 Azure 儲存體帳戶 (在您設定幾乎有無限儲存空間的 Azure 型檔案伺服器之後)，或是使用 Azure 備份保存庫或 Azure 冷儲存體。 另一個選項是在備份複製到儲存體帳戶之後，使用協力廠商資料保護工具 (例如 Commvault) 來儲存備份。 針對因合規性和稽核目的而需要較長儲存期的資料，DIY 備份選項可能也有其必要性。
- 使用 SAP HANA on Azure (大型執行個體) 的底層基礎結構所提供的備份和還原功能。 此選項可滿足備份需求，使得手動備份幾乎成為過時選項 (因合規性目的而需要資料備份的情況除外)。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。

> [!NOTE]
> HANA (大型執行個體) 的底層基礎結構所使用的快照集技術相依於 SAP HANA 快照集。 SAP HANA 快照集無法搭配 SAP HANA 多租用戶資料庫容器一起使用。 因此，這種備份方法無法用於部署 SAP HANA 多租用戶資料庫容器。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 SAP HANA on Azure (大型執行個體) 的儲存體快照集

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區儲存體快照集的概念。 不論是備份還是還原特定磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行資料庫備份。
- 儲存體快照集會先起始 SAP HANA 快照集，然後才執行儲存體快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。
- 在順利執行儲存體快照集的地方會刪除 SAP HANA 快照集。
- 系統會經常建立記錄備份並儲存在記錄備份磁碟區或 Azure 中。
- 如果資料庫必須還原到特定的時間點，系統就會對「Microsoft Azure 支援」(生產環境中斷) 或「SAP HANA on Azure 服務管理」發出要求，以還原到特定的儲存體快照集 (例如，將沙箱系統還原到其原始狀態的計劃性還原)。
- 儲存體快照集所包含的 SAP HANA 快照集是一個位移點，用來套用在儲存體快照集建立後已執行和儲存的記錄備份。
- 建立這些記錄備份的目的是要將資料庫還原回特定的時間點。

指定 backup\_name 會建立下列磁碟區的快照集：

- hana/data
- hana/log
- hana/log\_backup (以備份形式掛接到 hana/log 中)
- hana/shared

### <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集「不是」免費提供的功能，因為必須配置額外的儲存空間。

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 特定的儲存體快照集 (在建立快照集的時間點) 耗用非常少的儲存體。
- 隨著在存放磁碟區上資料內容發生變更及 SAP HANA 資料檔中的內容發生變更，快照便需要儲存原始區塊內容。
- 儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會吞噬您的磁碟區空間，因此是由您負責排定儲存體快照集 (在 SAP HANA on Azure [大型執行個體] 程序內)。

下列各節提供執行這些快照集的資訊，包括一般建議事項：

- 雖然硬體的每一磁碟區都可支援 255 個快照集，但強烈建議您讓快照集數目保持遠低於這個數字。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可能需要降低保留的快照集數目，也可能需要擴充磁碟區  (您可以訂購額外的儲存空間，以 1-TB 為單位)。
- 在進行活動的期間，例如使用系統移轉工具 (使用 R3load) 將資料移到 SAP HANA (或是從備份還原 SAP HANA 資料庫)，強烈建議您不要執行任何儲存體快照集  (如果系統移轉是在新的 SAP HANA 系統上執行，則不需要執行儲存體快照集)。
- 在進行較大規模的 SAP HANA 資料表重組期間，應儘可能避免執行儲存體快照。
- 儲存體快照集也是納入 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

### <a name="setting-up-storage-snapshots"></a>設定儲存體快照

1. 確定 HANA (大型執行個體) 伺服器上的 Linux 作業系統中已安裝 Perl。
2. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
3. 針對您目前執行的每個 SAP HANA 執行個體，在主要節點上建立 SAP HANA 備份使用者帳戶 (如果適用)。
4. 所有 SAP HANA (大型執行個體) 伺服器上都必須安裝 SAP HANA HDB 用戶端。
5. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上都必須建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
6. 將指令碼 azure\_hana\_backup.pl 從 /scripts 複製到 SAP HANA 安裝項的 **hdbsql** 位置。
7. 將 HANABackupDetails.txt 檔案從 /scripts 複製到與 Perl 指令碼相同的位置。
8. 視需要修改 HANABackupDetails.txt 檔案以符合適當的客戶規格。

### <a name="step-1-install-sap-hana-hdbclient"></a>步驟 1：安裝 SAP HANA HDBClient

安裝在 SAP HANA on Azure (大型執行個體) 上的 Linux 包含針對備份和災害復原目的執行 SAP HANA 儲存體快照集時所需的資料夾與指令碼。 不過，安裝 SAP HANA 時，是由您負責安裝 SAP HANA HDBclient (Microsoft 既不會安裝 HDBclient 也不會安裝 SAP HANA)。

### <a name="step-2-change-etcsshsshconfig"></a>步驟 2：變更 /etc/ssh/ssh\_config

在 /etc/ssh/ssh\_config 中新增 _MACs hmac-sha1_ 這一行，如下所示：
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>步驟 3︰建立公開金鑰

在每個 Azure 區域的第一部 SAP HANA on Azure (大型執行個體) 伺服器上，建立用來存取儲存體基礎結構的公開金鑰，讓您能夠建立快照集。 此公開金鑰可確保不需密碼即可登入儲存體，且不需維護密碼認證。 在 SAP HANA (大型執行個體) 伺服器上的 Linux 中，請執行下列命令來產生公開金鑰：
```
  ssh-keygen –t dsa –b 1024
```
新的位置是 _/root/.ssh/id\_dsa.pub。 請勿輸入實際的複雜密碼，否則您每次登入時都需要輸入複雜密碼。 改為按 **Enter** 兩次，在登入時就不需要輸入複雜密碼。

將資料夾切換至 /root/.ssh/，然後執行 **ls** 命令，以確定已如預期地更正公開金鑰。 如果金鑰存在，您可以執行下列命令來複製它︰

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此時，請連絡「SAP HANA on Azure 服務管理」並提供金鑰。 服務代表會使用公開金鑰，將它註冊在底層儲存體基礎結構中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶以供備份使用。 此帳戶必須具備下列權限：「備份管理」和「目錄讀取」。 在此範例中，建立的使用者名稱是 SCADMIN。

![在 HANA Studio 中建立使用者](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

授權 SAP HANA 使用者帳戶 (供指令碼使用，讓指令碼不需在每次執行時都要求授權)。 SAP HANA 命令 `hdbuserstore` 能讓您建立儲存在一或多個 SAP HANA 節點上的 SAP HANA 使用者金鑰。 使用者金鑰也可讓使用者存取 SAP HANA，而不需要從指令碼程序內管理密碼 (稍後討論)。

>[!IMPORTANT]
>以 `_root_` 身分執行下列命令。 否則，指令碼無法正常運作。

輸入 `hdbuserstore` 命令，如下所示︰

![輸入 hdbuserstore 命令](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

在使用者為 SCADMIN01 而主機名稱為 lhanad01 的以下範例中，命令是：
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
針對相應放大 HANA 執行個體，從單一伺服器管理所有指令碼處理。 在此範例中，必須針對每個主機改變 SAP HANA 金鑰 SCADMIN01，以反映與金鑰相關的主機。 也就是說，以 HANA DB **lhanad** 的執行個體數目修訂 SAP HANA 備份帳戶。 此金鑰必須具備所被指派主機上的系統管理權限，而相應放大的備份使用者則必須具備所有 SAP HANA 執行個體的存取權限。
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>步驟 6：從 /scripts 資料夾複製項目

將下列項目從 /scripts 資料夾 (包含在主要安裝映像中) 複製到 **hdbsql** 的工作目錄。 就目前的 HANA 安裝而言，這個目錄是 /hana/shared/D01/exe/linuxx86\_64/hdb。
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
複製下列項目 (如果它們執行的是相應放大或 OLAP)：
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
您可以依下列方式修改 HANABackupCustomerDetails.txt 檔案以進行相應放大部署。 它是執行儲存體快照集之指令碼的控制及組態檔。 當您的執行個體部署完成時，您應該會從「SAP HANA on Azure 服務管理」收到「儲存體備份名稱」和「儲存體 IP 位址」。 您不能修改任何變數的順序、排序或間距，否則指令碼將無法正常執行。

就相應增加部署而言，組態檔看起來會像這樣：
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
就相應放大組態而言，HANABackupCustomerDetailsBW.txt 檔案看起來會像這樣：
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>目前，實際 HANA 儲存快照集指令碼中只使用節點 1 詳細資料。 我們建議您往返所有 HANA 節點來測試存取，以便如果主要備份節點變更，您就已確定任何其他節點可以修改節點 1 中的詳細資料，以接替此節點。

若要檢查組態檔中的設定是否正確，或是否可正常連接至 HANA 執行個體，請執行下列任何指令碼︰
- 如果是相應增加組態 (與 SAP 工作負載無關)︰

 ```
testHANAConnection.pl
```
- 如果是相應放大組態：

 ```
testHANAConnectionBW.pl
```

請確保主要 HANA 執行個體能夠存取所有必要的 HANA 伺服器。 指令碼並無參數，但您必須完成適當的 HANABackupCustomerDetails/ HANABackupCustomerDetailsBW 檔案，指令碼才能正常執行。 因為只傳回殼層命令錯誤碼，所以指令碼無法檢查每個執行個體的錯誤。 即便如此，指令碼會提供一些實用的意見讓您再次檢查。

執行指令碼：
```
 ./testHANAConnection.pl
```
 如果指令碼成功取得 HANA 執行個體的狀態，則會顯示 HANA 連接成功的訊息。

此外，還有第二種指令碼可讓您檢查主要 HANA 執行個體伺服器是否能夠登入儲存體。 執行 azure\_hana\_backup(\_bw).pl 指令碼之前，您必須先執行下一個指令碼。 如果磁碟區不含快照集，則無法判斷磁碟區是否只是空白，還是因為 ssh 失敗而無法取得快照集詳細資料。 因此，指令碼會執行兩個步驟：

- 確認可存取儲存體主控台。
- 依 HANA 執行個體，為每個磁碟區建立測試 (或虛設) 快照集。

基於這個理由，會包含 HANA 執行個體作為引數。 同樣地，無法提供儲存體連接方面的錯誤檢查，但指令碼會在執行失敗時提供有用的提示。

指令碼的執行方式為：
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
或執行為︰
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
指令碼也會顯示訊息，指出您可以正常登入已部署的儲存體租用戶，此租用戶是依您擁有的伺服器執行個體所使用的邏輯單元數目 (LUN) 來組織。

在您執行第一個以儲存體快照集為基礎的備份之前，請執行下列指令碼，以確定組態正確。

在執行這些指令碼之後，您可以執行下列指令碼來刪除快照集：
```
./removeTestStorageSnapshot.pl <hana instance>
```
或
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>步驟 7：執行隨選快照集

執行隨選快照集 (以及使用 cron 來排程一般快照集） 如下所示。

如果是相應增加組態，請執行下列指令碼︰
```
./azure_hana_backup.pl lhanad01 customer 20
```
如果是相應放大組態，請執行下列指令碼︰
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
相應放大指令碼會先執行一些額外的檢查，以確定所有 HANA 伺服器皆可供存取，且所有 HANA 執行個體皆傳回適當的執行個體狀態，然後才繼續建立 SAP HANA 或儲存體快照集。

下列是必要引數：

- 需要備份的 HANA 執行個體。
- 儲存體快照的快照前置詞。
- 針對特定前置詞要保留的快照數目。

```
./azure_hana_backup.pl lhanad01 customer 20
```

執行此指令碼會以下列三個不同的階段建立儲存體快照：

- 執行 HANA 快照。
- 執行儲存體快照。
- 移除 HANA 快照。

從指令碼複製到 HDB 可執行檔資料夾中，經由呼叫來執行指令碼。 它會至少備份下列磁碟區，但也會備份磁碟區名稱中有明確 SAP HANA 執行個體名稱的任何磁碟區。
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
保留期完全取決於您執行指令碼時以參數送出的快照集數目 (例如上面所示的 20)。 因此，時間長度取決於執行期間及指令碼呼叫中的快照集數目。 如果所保留的快照集數目超出指令碼呼叫中以參數指定的數目，將會先刪除此標籤的最舊儲存體快照集 (在前一個的案例中為 _custom_)，然後才執行新的快照集。 這意謂著您所提供作為最後呼叫參數的數字，就是您可用來控制快照數目的數字。

>[!NOTE]
>只要您一變更標籤，就會重新開始計算。

您必須包含「SAP HANA on Azure 服務管理」所提供的 HANA 執行個體名稱作為引數 (如果它們在多節點環境中建立快照集的話)。 在單一節點環境中，只要有 SAP HANA on Azure (大型執行個體) 單位的名稱即已足夠，但仍建議使用 HANA 執行個體名稱。

此外，您可以使用相同的指令碼來備份開機磁碟區\LUN。 第一次執行 HANA 時，您必須至少備份開機磁碟區一次，但建議以 cron 進行每週或每晚開機備份排程。 不必新增 SAP HANA 執行個體名稱，只要將 _boot_ 當作引數插入指令碼中即可，如下所示：
```
./azure_hana_backup boot customer 20
```
相同的保留原則也同樣適用於開機磁碟區。 使用隨選快照集 (如先前所述) 應該僅用於特殊情況，例如在 SAP 增強套件 (EHP) 升級期間，或當您需要建立個別儲存體快照集時。

建議您使用 cron 來執行排定的儲存體快照集，也建議您針對所有備份和災害復原需求使用相同的指令碼 (修改指令碼輸入以符合所要求的不同備份時間)。 這些在 cron 中都會根據其執行時間以不同方式排定：每小時、12 小時、每天或每週。 cron 排程的設計目的是要針對長期異地備份，建立符合先前所述之保留標籤的儲存體快照集。 此指令碼包含可依據要求的頻率備份所有生產磁碟區的命令 (資料和記錄檔會每小時備份一次，而開機磁碟區則每天備份一次)。

以下 cron 指令碼中的項目會在每小時的第 10 分鐘、每 12 小時的第 10 分鐘及每天的第 10 分鐘執行。 cron 作業是以在任何特定時間只執行一個 SAP HANA 儲存體快照集的方式建立，因此每小時和每天備份不會同時 (12:10 AM) 發生。 為了協助將快照集建立和複寫最佳化，「SAP HANA on Azure 服務管理」為您提供執行備份的建議時間。

/etc/crontab 中的預設 cron 排程如下：
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
在先前的 cron 指示中，HANA 磁碟區 (不含開機磁碟區) 會取得含有標籤的每小時快照集。 在這些快照中，會保留 66 個。 此外會保留 14 個含有 12 小時標籤的快照集。 您可能取得三天的每小時快照集，加上另外四天的 12 小時快照集，您就有整週的快照集。

在 cron 內進行排程可能需要一些技巧，因為在任何特定時間應該都只執行一個指令碼，除非以數分鐘的時間錯開指令碼。 如果您想要每天備份以供長期保留，則不是將每天快照集與 12 小時快照集一起保留 (保留計數各為 7)，就是將每小時快照集錯開成晚 10 分鐘發生。 在生產磁碟區中只會保留一個每天快照集。
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
此處所列的頻率只是範例。 如果要找出最佳的快照集數目，請使用下列準則：

- 時間點復原的復原時間目標中的需求。
- 空間使用量。
- 潛在災害復原的復原點目標和復原時間目標中的需求。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 _backint_ 介面時，執行儲存體快照集都會失敗。 如果除了儲存體快照集之外，您還打算執行完整資料庫備份，請確定在此期間停用儲存體快照集的執行。

>[!IMPORTANT]
> 只有在快照集與 SAP HANA 記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有效。 這些記錄備份必須要能夠涵蓋儲存體備份之間的時段。 如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 能夠存取達 30 天的儲存體快照。
- 過去 30 天的連續記錄備份。

在記錄備份的範圍中，一併建立備份記錄磁碟區的快照集。 不過，請務必執行定期記錄備份，以便您可以︰

- 擁有執行時間點復原所需的連續記錄備份。
- 防止 SAP HANA 記錄磁碟區用盡空間。

其中一個最後步驟是在 SAP HANA Studio 中排定 SAP HANA 備份記錄。 SAP HANA 備份記錄目標目的地是特別建立的 hana/log\_backups 磁碟區，其中掛接點為 /hana/log/backups。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您可以選擇比每隔 15 分鐘更頻繁的備份。 有些使用者甚至每分鐘都執行記錄備份，但我們不建議「超過」15 分鐘。

最後一個步驟是執行檔案型備份 (在初始安裝 SAP HANA 之後)，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁碟區上的快照集數目和大小

在特定的存放磁碟區上，您可以監視快照的數目和快照的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 不過，Linux OS 命令 `du`就可以，但需搭配下列命令：

- `du –sh .snapshot` 提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1` 列出所有儲存在 .snapshot 資料夾中的快照集及每個快照集的大小。
- `du –hc` 提供所有快照集使用的總大小。

您可以使用這些命令來確定所建立和儲存的快照集並未耗用磁碟區上的所有儲存體。

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>縮減伺服器上的快照數目

如先前所述，您可以針對您儲存的快照集，減少某些標籤的數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。
```
./azure_hana_backup.pl lhanad01 customer 20
```
在前一個範例中，快照集標籤是 _customer_，而此標籤內要保留的快照集數目則是 _20_。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 減少快照集數目的簡單方法是將最後一個參數設定為 5 來執行指令碼：
```
./azure_hana_backup.pl lhanad01 customer 5
```
以這項設定執行指令碼之後，快照集數目 (包括新的儲存體快照集) 會是 _5_。

 >[!NOTE]
 > 最近的上一個快照集必須是過去一小時以上，此指令碼才會減少快照集數目。 指令碼不會刪除過去一小時內的快照集。

這些限制與所提供的選擇性災害復原功能有關。

如果您不想再維護一組含有該前置詞的快照集，您可以使用 _0_ 作為保留數字來執行指令碼，以移除所有符合該前置詞的快照集。 不過，移除所有快照集可能會影響災害復原的功能。

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

當您遇到生產環境停止運作的情況時，可以向「SAP HANA on Azure 服務管理」以「客戶事件」的形式起始從儲存體快照集復原的程序。 如果生產系統中的資料遭到刪除，而挽救資料的唯一方法是還原生產資料庫，則這種非預期的情況可能是高緊急性的事件。

反之，時間點復原則是低緊急性，可提前幾天來規劃。 您可以使用「SAP HANA on Azure 服務管理」來規劃此復原，以避免引發高優先性的問題。 例如，您可能打算套用新的增強套件來嘗試 SAP 軟體升級，然後需要還原回代表 EHP 升級前狀態的快照集。

發出要求之前，您需要做一些準備工作。 接著，「SAP HANA on Azure 服務管理」小組就可以處理該要求，並提供已還原的磁碟區。 之後，由您負責根據快照集還原 HANA 資料庫。 以下是在要求之前如何做好準備︰

>[!NOTE]
>視您使用的 SAP HANA 版本而定，您的使用者介面可能與下列螢幕擷取畫面不同。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。

2. 關閉 HANA 執行個體。

 ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果未卸載資料磁碟區，則快照集還原會失敗。

 ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 提出 Azure 支援要求，以取得還原特定快照集的指示。

 - 還原期間︰「SAP HANA on Azure 服務管理」可能會要求您參加電話會議，以確保沒有遺失任何資料。

 - 還原之後：還原儲存體快照集之後，「SAP HANA on Azure 服務管理」會通知您。

5. 在還原程序完成後，重新掛接所有資料磁碟區。

 ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. 當您透過 SAP HANA Studio 重新連接到 HANA DB 時，如果沒有自動出現復原選項，請選取 SAP HANA Studio 內的復原選項。 以下範例顯示還原到最後一個 HANA 快照集。 儲存體快照集內嵌一個 HANA 快照集，如果您要復原到最近的儲存體快照集，它應該是最近的 HANA 快照集。 (如果您要復原到較舊的儲存體快照，您就必須根據儲存體快照的建立時間找出 HANA 快照)。

 ![選取 SAP HANA Studio 內的復原選項](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 選取 [將資料庫復原到特定資料備份或儲存體快照集]。

 ![[指定復原類型] 視窗](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. 選取 [指定不含目錄的備份]。

 ![[指定備份位置] 視窗](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. 在 [目的地類型] 清單中，選取 [快照集]。

 ![[指定要復原的備份] 視窗](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. 按一下 [完成] 以開始復原程序。

 ![按一下 [完成] 以開始復原程序](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 資料庫會還原並復原到儲存體快照集所包含的 HANA 快照集。

 ![HANA 資料庫會還原並復原到 HANA 快照集](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>復原到最近狀態

下列程序會還原儲存體快照集所包含的 HANA 快照集。 然後，將交易記錄備份還原至資料庫在還原儲存體快照集之前的最近狀態。

>[!IMPORTANT]
>繼續進行之前，請先確定您擁有一串完整且連續的交易記錄備份。 如果沒有這些備份，您就無法還原資料庫的目前狀態。

1. 完成「復原到最近的 HANA 快照集 」中先前程序的步驟 1-6。

2. 選取 [將資料庫復原到最近狀態]。

 ![選取 [將資料庫復原到最近狀態]。](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 指定最近 HANA 記錄備份的位置。 此位置必須包含從 HANA 快照集到最近狀態的所有 HANA 交易記錄備份。

 ![指定最近 HANA 記錄備份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 選取一個備份作為基礎來從中復原資料庫。 在我們的範例中，這是儲存體快照所包含的 HANA 快照。 (下列螢幕擷取畫面只列出一個快照集)。

 ![選取一個備份作為基礎來從中復原資料庫](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 如果 HANA 快照集快時間與最近狀態之間沒有差異，請清除 [使用差異備份] 核取方塊。

 ![如果沒有差異，請清除 [使用差異備份] 核取方塊](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 在摘要畫面上，按一下 [完成] 以開始還原程序。

 ![在摘要頁面上按一下 [完成]](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>復原到另一個時間點
若要復原到介於 HANA 快照集 (包含在儲存體快照集中) 與晚於 HANA 快照集時間點復原的快照集之間的時間點，請執行下列作業：

1. 確定您擁有包含從 HANA 快照到您想要復原之時間在內的所有 HANA 交易記錄備份。
2. 開始執行 [復原到最近狀態] 下的程序。
3. 在程序的步驟 2 中，在 [指定復原類型] 視窗中選取 [將資料庫復原到下列時間點]，指定時間點，然後完成步驟 3-6。

## <a name="monitoring-the-execution-of-snapshots"></a>監視快照集的執行

您需要監視儲存體快照集的執行。 執行儲存體快照集的指令碼會將輸出寫入檔案，然後儲存至與 Perl 指令碼相同的位置。 針對每個快照都會寫入一個個別的檔案。 每個檔案的輸出會清楚顯示快照集指令碼所執行的各階段︰

- 尋找需要建立快照集的磁碟區
- 尋找從這些磁碟區建立的快照集
- 刪除最後存在的快照集以符合您指定的快照集數目
- 建立 HANA 快照集
- 建立所有磁碟區的儲存體快照集
- 刪除 HANA 快照集
- 將最近的快照集重新命名為 **.0**

指令碼的最重要部分是：
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
您可以從此範例看到指令碼如何記錄 HANA 快照的建立。 在相應放大案例中，是在主要節點上起始此程序。 主要節點會在每個背景工作角色節點上起始同步建立快照集。 然後建立儲存體快照。 順利執行儲存體快照之後，就會刪除 HANA 快照。

