---
title: "設定 StorSimple 搭配 Veritas NetBackup | Microsoft Docs"
description: "描述搭配 Veritas NetBackup 的 StorSimple 備份目標組態。"
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: 45fc96674d52a1e1273eed390195c59889b86a38
ms.openlocfilehash: 50c4be545f3b852d99968548784ffdff048d5355

---

# <a name="configure-storsimple-with-veritas-netbackup8482"></a>設定 StorSimple 搭配 Veritas NetBackup&#8482;

## <a name="overview"></a>概觀

Microsoft Azure StorSimple 是一個混合式雲端儲存體解決方案，使用 Azure 儲存體帳戶做為內部部署解決方案的擴充功能，跨內部部署儲存體和雲端儲存體自動將資料分層，解決資料暴增的複雜性問題。

本文討論 StorSimple 整合 Veritas NetBackup，以及整合兩種解決方案的最佳作法。 我們也會建議如何設定 Veritas NetBackup 與 StorSimple 進行最佳的整合。 我們遵循 Veritas 的最佳作法，並聽從備份架構設計人員和管理員關於如何妥善地設定 Veritas NetBackup 以符合您的備份需求和 SLA 的意見。


本文說明設定步驟和重要概念，但這不是逐步的設定或安裝指南。 本文假設基本元件和基礎結構都已正常運作，並準備好支援我們所描述的概念。

## <a name="why-storsimple-as-a-backup-target"></a>為什麼使用 StorSimple 做為備份目標？

StorSimple 因為下列原因是一個很好的備份目標︰

-   為備份應用程式提供標準的本機儲存體，而且不需要任何變更就可提供快速備份的目的地。 StorSimple 也可用來快速還原最新的備份。

-   其雲端分層可與雲端儲存體帳戶緊密整合，使用經濟實惠的 Microsoft Azure 儲存體。

-   自動提供可供災害復原的異地儲存體。


## <a name="target-audience"></a>目標對象

本白皮書的對象包括了解儲存體、Windows Server 2012 R2、乙太網路、雲端服務和 NetBackup 等知識的備份管理員、儲存體管理員及儲存體架構設計人員。

## <a name="supported-versions"></a>支援的版本

-   NetBackup 7.7.x 或更新版本。

-   [StorSimple Update 3 及更新版本](storsimple-overview.md#storsimple-workload-summary)。

## <a name="key-concepts"></a>重要概念

如同任何其他儲存體解決方案，仔細評估解決方案的儲存體效能、SLA、變更速率及容量成長需求是成功與否的關鍵。 主要概念是藉由引進雲端層，對雲端的存取時間和輸送量就能對 StorSimple 執行其工作的能力扮演重要角色。

StorSimple 的設計目的是針對運作定義完善的使用中資料集 (熱資料) 的應用程式提供儲存體。 在此模型中，使用中的資料集儲存在本機層，其餘非使用中/冷/封存的資料集則分層儲存到雲端。 下圖說明這個模型。 幾乎是平坦的綠線表示儲存在 StorSimple 裝置本機層的資料。 紅線則表示儲存在 StorSimple 解決方案所有各層的資料總量。 平坦綠線與呈指數形的紅色曲線之間的空間代表儲存在雲端的資料總量。

**StorSimple 分層**
![Storsimple 分層圖](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)


記住此架構，您會發現 StorSimple 非常適合做為備份目標。 StorSimple 可讓您︰

-   從本機使用中的資料集執行最頻繁的還原。

-   對於異地災害復原和較少進行還原的舊資料使用雲端。

## <a name="storsimple-benefits"></a>StorSimple 的優點

StorSimple 提供的內部部署解決方案，可以不間斷存取內部部署和雲端儲存體，與 Microsoft Azure 緊密整合。

StorSimple 在包含固態裝置 (SSD) 和序列連接 SCSI (SAS) 儲存體的內部部署裝置與 Azure 儲存體之間使用自動分層。 自動分層將經常存取的資料保存在本機的 SSD 和 SAS 層，並將不常存取的資料移到 Azure 儲存體。

StorSimple 提供下列優點︰

-   獨家的重複資料刪除和壓縮演算法，使用雲端以達到前所未有的重複資料刪除層級

-   高可用性

-   使用 Azure 異地複寫進行異地複寫

-   與 Azure 整合

-   雲端中的資料會進行加密

-   改進災害復原和提高合規性

雖然 StorSimple 展示兩個主要的部署案例 (主要和次要備份目標)，但請記得它基本上只是一般的區塊存放裝置。 StorSimple 會進行所有的壓縮和重複資料刪除工作，而且會順暢地擷取雲端的資料並傳送到應用程式和檔案系統。

如需 StorSimple 的詳細資訊，請參閱 [StorSimple 8000 系列︰混合式雲端儲存體解決方案](storsimple-overview.md)並檢閱 [StorSimple 8000 系列技術規格](storsimple-technical-specifications-and-compliance.md)。


> [!IMPORTANT] 
> 只有 StorSimple 8000 Update 3 或更新版本才支援 StorSimple 裝置做為備份目標。


## <a name="architecture-overview"></a>架構概觀

下表包含設備機型與架構的初始指導方針。

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>StorSimple 的本機和雲端儲存體容量


| 儲存體容量       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| 本機儲存體容量 | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| 雲端儲存體容量 | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* 儲存體大小假設沒有重複資料刪除或壓縮。

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>StorSimple 的主要和次要備份容量


| 備份案例  | 本機儲存體容量                                         | 雲端儲存體容量                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| 主要備份   | 最近的備份會儲存在本機儲存體供快速復原 (RPO)。 | 備份歷程記錄 (RPO) 可放入雲端容量。 |
| 次要備份 | 備份資料的次要複本可儲存在雲端容量。  |

## <a name="storsimple-as-a-primary-backup-target"></a>使用 StorSimple 做為主要備份目標

在此案例中，備份應用程式會使用 StorSimple 磁碟區，做為備份的唯一存放庫。 下圖說明解決方案架構，其中所有備份都使用 StorSimple 分層磁碟區進行備份和還原。

![使用 StorSimple 做為主要備份目標的邏輯圖](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>主要目標備份的邏輯步驟

1.  備份伺服器連絡目標備份代理程式，然後備份代理程式將資料傳輸到備份伺服器。

2.  備份伺服器將資料寫入 StorSimple 分層磁碟區。

3.  備份伺服器更新類別目錄資料庫，並完成備份作業。

4.  快照集指令碼觸發 StorSimple 雲端快照集管理 (開始刪除)。

5.  備份伺服器根據保留原則刪除過期的備份。

###  <a name="primary-target-restore-logical-steps"></a>主要目標還原的邏輯步驟

1.  備份伺服器開始從存放庫還原適當的資料。

2.  備份代理程式接收來自備份伺服器的資料。

3.  備份伺服器完成還原作業。

## <a name="storsimple-as-a-secondary-backup-target"></a>使用 StorSimple 做為次要備份目標

在此案例中，大部分的長期保留或封存主要都使用 StorSimple 磁碟區。

下圖說明初始備份和還原以高效能磁碟區做為目標的架構。 這些備份會以指定的排程複製並封存至 StorSimple 分層磁碟區。

請務必讓您的高效能磁碟區具有足夠的空間和效能，以處理保留原則容量和效能需求。

![使用 StorSimple 做為次要備份目標的邏輯圖](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>次要目標備份的邏輯步驟

1.  備份伺服器連絡目標備份代理程式，然後備份代理程式將資料傳輸到備份伺服器。

2.  備份伺服器將資料寫入高效能儲存體。

3.  備份伺服器更新類別目錄資料庫，並完成備份作業。

4.  備份伺服器根據保留原則將備份複製到 StorSimple。

5.  快照集指令碼觸發 StorSimple 雲端快照集管理 (開始刪除)。

6.  備份伺服器根據保留原則刪除過期的備份。

### <a name="secondary-target-restore-logical-steps"></a>次要目標還原的邏輯步驟

1.  備份伺服器開始從存放庫還原適當的資料。

2.  備份代理程式接收來自備份伺服器的資料。

3.  備份伺服器完成還原作業。

## <a name="deploy-the-solution"></a>部署解決方案

部署這個解決方案有三個步驟︰準備網路基礎結構、部署 StorSimple 裝置做為備份目標，最後部署 Veritas NetBackup。 下列各節會詳細討論這些步驟。

### <a name="configure-the-network"></a>設定網路

要使用 StorSimple 做為與 Azure 雲端整合的解決方案，需要作用中並正常運作的 Azure 雲端連線。 此連線供雲端快照集、管理、中繼資料傳輸等操作使用，以及將較舊、較少存取的資料分層儲存到 Azure 雲端儲存體。

為了能以最佳方式執行解決方案，我們建議您遵循下列網路最佳作法︰

-   StorSimple 分層連接至 Azure 的連結必須符合頻寬需求，您可以對基礎結構的交換器套用適當的服務品質 (QoS) 以符合 RPO/RTO SLA。

-   Azure Blob 儲存體的最大存取延遲時間應該在 80 毫秒的範圍內。

### <a name="deploy-storsimple"></a>部署 StorSimple

如需 StorSimple 部署的逐步指引，請移至[部署內部部署 StorSimple 裝置](storsimple-deployment-walkthrough-u2.md)。

### <a name="deploy-netbackup"></a>部署 NetBackup

如需逐步解說的 NetBackup 7.7.x 部署指南，請參閱 [NetBackup 7.7.x 文件 (英文)](https://www.veritas.com/support/article.000094423)

## <a name="configure-the-solution"></a>設定解決方案

在本節中，我們會示範一些組態範例。 下列範例/建議說明最基本也最重要的實作。 此實作可能無法直接套用到您特定的備份需求。

### <a name="configure-storsimple"></a>設定 StorSimple

| StorSimple 部署工作                                                                                                                 | 其他註解                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 部署內部部署 StorSimple 裝置                                                                                                 | 支援的版本︰Update 3 和更新版本。                                                                                                                                                                                                                                                                 |
| 啟用備份目標模式                                                                                                                   | 使用下列命令來啟用/停用及取得狀態。 如需詳細資訊，請移至[遠端連線至 StorSimple](storsimple-remote-connect.md)。</br> 啟用備份模式：`Set-HCSBackupApplianceMode -enable`</br>  停用備份模式：`Set-HCSBackupApplianceMode -disable`</br> 備份模式設定的目前狀態：`Get-HCSBackupApplianceMode` |
| 為儲存備份資料的磁碟區建立一般的磁碟區容器。   磁碟區容器中的所有資料都已刪除重複資料。 | StorSimple 磁碟區容器定義重複資料刪除網域。                                                                                                                                                                                                                                             |
| 建立 StorSimple 磁碟區                                                                                                                 | 建立大小盡可能接近預期使用量的磁碟區，因為磁碟區大小會影響雲端快照集的持續時間。 如需有關如何設定磁碟區大小的詳細資訊，請移至[保留原則](#retention-policies)。</br> </br> 使用 StorSimple 分層磁碟區，並核取 [使用此磁碟區存放不常存取的封存資料]。 </br> 不支援只固定在本機的磁碟區。|
| 為所有備份目標磁碟區建立唯一的 StorSimple 備份原則。                                                               | StorSimple 備份原則定義磁碟區一致性群組。                                                                                                                                                                                                                                       |
| 在快照集到期時停用排程。                                                                                                    | 後處理作業會觸發快照集。                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |



### <a name="configure-host-backup-server-storage"></a>設定主機備份伺服器儲存體

確定主機備份伺服器儲存體已根據下列指導方針進行設定。  

- 不支援合併磁碟區 (由 Windows 磁碟管理員所建立)。
- 使用 64 KB 配置單位大小的 NTFS 格式化磁碟區。
- 將 StorSimple 磁碟區直接對應到 NetBackup 伺服器。 
    - 為實體伺服器使用 iSCSI。
    - 虛擬伺服器請使用傳遞磁碟。


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple 和 NetBackup 的最佳作法

根據下列指導方針設定您的解決方案。

### <a name="operating-system"></a>作業系統

-   停用 NTFS 檔案系統的 Windows Server 加密和重複資料刪除。

-   停用 StorSimple 磁碟區的 Windows Server 磁碟重組。

-   停用 StorSimple 磁碟區的 Windows Server 索引。

-   在來源主機 (不是針對 StorSimple 磁碟區) 執行防毒軟體掃描。

-   在工作管理員中停用預設的 [Windows Server 維護 (英文)](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)。

    - 在 Windows 工作排程器中停用維護設定程式。

        或

    - 下載︰[PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - 下載 PSEXEC 之後，請以系統管理員身分執行 Windows PowerShell 並輸入：

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   確定 StorSimple 裝置已更新為 [Update 3 或更新版本](storsimple-install-update-3.md)。

-   隔離 iSCSI 和雲端流量。 針對 StorSimple 和備份伺服器之間的流量使用專用的 iSCSI 連線。

-   確定 StorSimple 裝置是專用的備份目標。 不支援混合的工作負載，因為這些工作負載會影響 RTO/RPO。

### <a name="netbackup"></a>NetBackup

-   NetBackup 資料庫應在伺服器本機，而且不在 StorSimple 磁碟區上。

-   如果要供 DR 之用，請將 NetBackup 資料庫備份在 StorSimple 磁碟區上。

-   我們支援此解決方案的 NetBackup 完整和增量備份。 我們建議您不要使用綜合和差異備份。

-   備份資料檔案最好只包含特定作業的資料。 例如，不允許在不同作業之間附加媒體。

如需詳細資訊，請參閱 [NetBackup 的文件 (英文)](https://www.veritas.com) 了解最新的 NetBackup 設定和如何實作這些需求的最佳作法。


## <a name="retention-policies"></a>保留原則

一個最常使用的備份保留原則是三代循環 (GFS)。 在此原則中，每天都會執行增量備份。 每週和每個月則會執行完整備份。 此原則會產生 6 個 StorSimple 分層磁碟區。

-   一個磁碟區包含每週、每個月和每年的完整備份。

-   其他五個磁碟區則儲存每日的增量備份。

在下列範例中，我們採用 GFS 循環。 範例的假設如下：

-   使用非重複資料刪除或壓縮的資料。

-   每個完整備份為 1 TiB。

-   每個每日增量備份為 500 GiB。

-   4 個每週備份保留一個月。

-   12 個每月備份保留一年。

-   1 個每年備份保留 10 年。

根據先前的假設，為每個月和每年的完整備份建立 26 TiB 的 StorSimple 分層磁碟區。 為每個每日增量備份建立 5 TiB 的 StorSimple 分層磁碟區。

| 備份類型保留期 | 大小 (TiB) | GFS 乘數\*                                       | 總容量 (TiB)          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 每週完整           | 1        | 4                                                      | 4                           |
| 每日增量     | 0.5      | 20 (循環數等於每個月的週數) | 12 (2 為其他配額) |
| 每月完整          | 1        | 12                                                     | 12                          |
| 每年完整           | 1        | 10                                                     | 10                          |
| GFS 需求       |          |                                                        | 38                          |
| 其他配額      | 4        |                                                        | 42 (總計 GFS 需求)   |

\*GFS 乘數是您需要保護並保留以符合備份原則的複本數目。

## <a name="configure-netbackup-storage"></a>設定 NetBackup 存放裝置


1.  在 NetBackup 管理主控台中，選取 [Devices (裝置)] \> 啟動 [Disk Pool Configuration Wizard (磁碟集區設定精靈)]\> 選取\> [AdvancedDisk]\>按一下 [Next (下一步)]

    ![NetBackup 管理主控台，磁碟集區設定](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

1.  選取您的伺服器。

    ![NetBackup 管理主控台，選取伺服器](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

1.  然後按 [Next (下一步)] 並選取您的 StorSimple 磁碟區

    ![NetBackup 管理主控台，選取 StorSimple 磁碟](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

1.  然後命名，並接連按兩次 [Next (下一步)] 完成

    ![NetBackup 管理主控台，名稱和描述畫面](./media/storsimple-configure-backup-target-using-netbackup/nbimage4.png)

    檢閱設定之後，按一下 [Finish (完成)]。

    1.  在每項指派作業結束時，將存放裝置設定變更成符合最佳作法清單中的建議。

    2.  重複步驟 1-4，直到完成指派 StorSimple 磁碟區。

    ![NetBackup 管理主控台，磁碟設定](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="storsimple-as-a-primary-backup-target"></a>使用 StorSimple 做為主要備份目標

> [!NOTE]
> 請注意，如果您需要從已經分層儲存到雲端的備份還原資料，還原會以雲端的速度進行。

在下圖中，我們說明如何將典型的磁碟區對應到備份作業。 在此情況下，所有的每週備份會對應到星期六完整的磁碟，增量備份則會對應到星期一至星期五的增量磁碟。 所有的備份和還原都會從 StorSimple 分層磁碟區進行。

![主要備份目標組態的邏輯圖 ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>使用 StorSimple 做為主要備份目標三代循環 (GFS) 的排程範例

| 4 週、每月和每年的 GFS 循環排程 |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 頻率/備份類型   | 完整          | 增量 (第 1 - 5 天)  |
| 每週 (第 1 - 4 週)    | 星期六 | 星期一 - 星期五 |
| 每月     | 星期六  |             |
| 每年      | 星期六  |             |


### <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>將 StorSimple 磁碟區指派給 NetBackup 備份作業

下列程序假設 NetBackup 和目標主機已依據 NetBackup 代理程式指導方針進行設定。

1.  在 NetBackup 管理主控台中，選取 [Policy (原則)] \> 以滑鼠右鍵按一下 \> [New Policy (新增原則)]

    ![NetBackup 管理主控台畫面](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

1.  選取原則名稱，然後選取 [Use Policy Configuration Wizard (使用原則設定精靈)]

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

1.  選取適當的備份類型，在我們的案例中為檔案系統，然後按 [Next (下一步)]

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

1.  然後選取 [Standard (標準)]

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

1.  選取您的主機，核取 [Detect client operating system (偵測用戶端作業系統)]，並新增

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

1.  然後按 [Next (下一步)]

2.  然後選取您要備份的磁碟機，在我們的案例中為 G:\\DataChange3\\

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

1.  選取符合您需求的循環

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

1.  接連按兩次 [Next (下一步)] 並完成。 建立原則之後，我們將修改排程。

2.  展開您所建立的原則，然後選取 [Schdules (排程)]。

    ![NetBackup 管理主控台，新增原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

1.  以滑鼠右鍵按一下 [Differential-Inc] 並選取 [Copy to new (複製到新的)]，在我們的案例中為 Mon-inc，然後按一下 [OK (確定)]

    ![NetBackup 管理主控台，新增原則排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

1.  然後以滑鼠右鍵按一下新建立的排程，並選取 [Change (變更)]。

2.  在 [Attributes (屬性)] 索引標籤中，核取 [Override policy storage selection (覆寫原則存放裝置選取項目)] 並選取星期一增量備份所儲存的磁碟區。 在我們的案例中為 SS1

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

1.  在 [Start Windows (開始時間範圍)] 中選取備份的時間範圍。 在我們的案例中為星期一的下午 8 點到下午 9 點

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

1.  選取 [OK (確定)]

2.  針對每個增量備份重複步驟 11-15，然後選取適當的磁碟區和排程。

3.  然後在 [Differential-inc] 排程上按一下滑鼠右鍵，並將它刪除。

4.  刪除 Differential-inc 排程之後，修改完整排程以符合您的需求。

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

1.  變更開始時間範圍，在我們的案例中為星期六上午 6 點

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

1.  排程最終應如下

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>使用 StorSimple 做為次要備份目標

> [!NOTE]
> 請注意，如果您需要從已經分層儲存到雲端的備份還原資料，還原會以雲端的速度進行。

在此模型中，您必須擁有儲存媒體 (而非 StorSimple) 做為暫時快取。 例如，您可以使用 RAID 磁碟區來容納空間、IO 和頻寬。 我們建議使用 RAID 5、50 和 10。

在下圖中，我們說明一般短期保留的 (相對於伺服器) 本機磁碟區，以及長期保留的封存磁碟區。 在此情況下，所有備份都會在 (相對於伺服器) 本機的 RAID 磁碟區上執行。 這些備份會定期複製並封存到封存磁碟區。 請務必設定您 (相對於伺服器) 本機的 RAID 磁碟區以處理短期保留容量和效能需求。

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>使用 StorSimple 做為次要備份目標 GFS 範例

![使用 StorSimple 做為次要備份目標的邏輯圖 ](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

下表說明如何設定備份在本機和 StorSimple 磁碟上執行，包含個人和總容量需求。

#### <a name="backup-configuration-and-capacity-requirements"></a>備份設定和容量需求

| 備份類型和保留期                    |設定的儲存體| 大小 (TiB) | GFS 乘數 | 總容量 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 第 1 週 (完整和增量) |本機磁碟 (短期)| 1        | 1              | 1           |
| StorSimple 第 2-4 週           |StorSimple 磁碟 (長期) | 1        | 4              | 4                   |
| 每月完整                                 |StorSimple 磁碟 (長期) | 1        | 12             | 12                   |
| 每年完整                               |StorSimple 磁碟 (長期) | 1        | 1              | 1                   |
|GFS 磁碟區大小需求 | |          |                | 18*|

\* 總容量包含 17 TiB 的 StorSimple 磁碟和 1 TiB 的本機 RAID 磁碟區


#### <a name="gfs-example-schedule"></a>GFS 範例排程

|每週、每月和每年排程的 GFS 循環|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 週                                                                     | 完整               | 增量 (第 1 天)        | 增量 (第 2 天)        | 增量 (第 3 天)        | 增量 (第 4 天)        | 增量 (第 5 天)        |
| 第 1 週                                                                   | 本機 RAID 磁碟區  | 本機 RAID 磁碟區 | 本機 RAID 磁碟區 | 本機 RAID 磁碟區 | 本機 RAID 磁碟區 | 本機 RAID 磁碟區 |
| 第 2 週                                                                   | StorSimple 第 2-4 週 |                   |                   |                   |                   |                   |
| 第 3 週                                                                   | StorSimple 第 2-4 週 |                   |                   |                   |                   |                   |
| 第 4 週                                                                   | StorSimple 第 2-4 週 |                   |                   |                   |                   |                   |
| 每月                                                                  | StorSimple 每月 |                   |                   |                   |                   |                   |
| 每年                                                                   | StorSimple 每年  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-netbackup-archiveduplication-job"></a>將 StorSimple 磁碟區指派給 NetBackup 封存/重複資料刪除作業。

由於 NetBackup 提的存放裝置和媒體管理有非常多的選項，請洽詢 Veritas 或 NetBackup 架構設計人員正確評估 SLP 需求。

1.  定義初始的磁碟集區之後，請繼續定義 3 個存放裝置生命週期原則︰

    1.  LocalRAIDVolume

    2.  StorSimpleWeek2-4

    3.  StorSimpleMonthlyFulls

    4.  StorSimpleYearlyFulls

    在管理主控台的 [Storage (存放裝置)] 下，選取 [Storage Lifecycle Policies (存放裝置生命週期原則)]，然後選取 [New Storage Lifecycle Policy (新增存放裝置生命週期原則)]

    [NetBackup 管理主控台，存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

1.  選取名稱，然後按一下 [Add (新增)]

    ![NetBackup 管理主控台，存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage21.png)

1.  在 [Properties (屬性)] 索引標籤中，選取 [Backup (備份)]、[Destination Storage (目的地存放裝置)]、適當的保留期及 [OK (確定)]。 在我們的案例中為 LocalRAIDVolume 和一週的保留期。

    ![NetBackup 管理主控台，存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

1.  這定義第一個備份作業/存放庫

2.  當反白顯示前一項作業時，按一下 [Add (新增)]，選取 [Destination Storage (目的地存放裝置)] 和適當的保留期。 在我們的案例中為從 LocalRAIDVolume 到 StorSimpleWeek2-4，以及 1 個月的保留期

    ![NetBackup 管理主控台，新增存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

1.  反白顯示前一項作業時，選取 [Add (新增)]，現在新增一年的每月備份

    ![NetBackup 管理主控台，變更存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

1.  重複步驟 5-6，直到有適當的 SLP 保留原則。

    ![NetBackup 管理主控台，存放裝置生命週期原則](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

1.  在您完成定義適當的 SLP 保留原則之後，在 [Policy (原則)] 下定義備份原則，如＜使用 StorSimple 做為主要目標＞一節所述。

1.  在 [Schedules (排程)] 下選取 [Full (完整)]，按一下滑鼠右鍵，然後選取 [Change (變更)]。

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

1.  選取 [Override policy storage selection (覆寫原則存放裝置選取項目)]，然後選取您在步驟 1-8 建立的 SLP

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

1.  按一下 [OK (確定)]，然後重複執行增量備份的排程。

    ![NetBackup 管理主控台，變更排程](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)

1.  按一下 [OK (確定)]，然後重複執行增量備份的排程。

| 備份類型保留期 | 大小 (TiB) | GFS 乘數\*                                       | 總容量 (TiB)          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 每週完整           | 1        | 4                                                      | 4                           |
| 每日增量     | 0.5      | 20 (循環數等於每個月的週數) | 12 (2 為其他配額) |
| 每月完整          | 1        | 12                                                     | 12                          |
| 每年完整           | 1        | 10                                                     | 10                          |
| GFS 需求       |          |                                                        | 38                          |
| 其他配額      | 4        |                                                        | 42 (總計 GFS 需求)。   |
\*GFS 乘數是您需要保護並保留以符合備份原則的複本數目。

### <a name="storsimple-cloud-snapshots"></a>StorSimple 雲端快照集

StorSimple 雲端快照集可保護位於 StorSimple 裝置中的資料。 這相當於將磁帶運送到異地場所，如果使用 Azure 異地備援儲存體 (GRS)，就是將磁帶運送到多個站台。 如果發生災害而需要還原裝置，可以讓另一個 StorSimple 裝置上線，並執行容錯移轉。 容錯移轉之後，您就可以從最新的雲端快照集 (以雲端速度) 存取資料。

下一節將說明如何建立簡短指令碼在備份後處理期間觸發和刪除 StorSimple 雲端快照集。

> [!NOTE]
> 以手動方式或以程式設計方式建立的快照集不會遵循 StorSimple 快照集到期原則。 這些快照集必須以手動方式或以程式設計方式刪除。

### <a name="start-delete-cloud-snapshots-with-a-script"></a>使用指令碼啟動和刪除雲端快照集

> [!NOTE]
> 在刪除 StorSimple 快照集之前，請先仔細評估合規性和資料保留的影響。 如需有關如何執行備份後指令碼的詳細資訊，請參閱 [NetBackup 文件 (英文)](https://www.veritas.com/support/article.000094423)。

#### <a name="backup-lifecycle"></a>備份生命週期

![備份生命週期圖表](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

#### <a name="requirements"></a>需求：

-   執行指令碼的伺服器必須能夠存取 Azure 雲端。

-   使用者帳戶必須擁有必要的權限。

-   設定但不要啟用與 StorSimple 磁碟區相關聯的 StorSimple 備份原則。

-   StorSimple 資源名稱、註冊金鑰、裝置名稱和備份原則識別碼。

#### <a name="steps"></a>步驟：

1.  [安裝 Azure PowerShell](/powershell-install-configure/)。

2.  [下載和匯入發佈設定和訂用帳戶資訊](https://msdn.microsoft.com/library/dn385850.aspx)

3.  在 Azure 傳統入口網站中，取得 StorSimple Manager 服務的資源名稱和註冊金鑰[](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)。

4.  在執行指令碼的伺服器上，以系統管理員身分執行 Windows PowerShell。 輸入：

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    記下備份原則識別碼。

5.  在記事本中，建立新的 Windows PowerShell 指令碼，並儲存在與儲存 Azure 發佈設定相同的位置。 例如， `C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1`。

    複製並貼上下列程式碼片段：

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  編輯 NetBackup 作業選項的前-後命令，在 NetBackup 的備份作業中新增指令碼

> [!NOTE]
> 我們建議您在每日備份作業結束時執行 StorSimple 雲端快照集備份原則，做為後處理指令碼。 如需有關如何備份和還原備份應用程式環境以符合 RPO/RTO 的詳細資訊，請洽詢您的備份架構設計人員。

## <a name="storsimple-as-a-restore-source"></a>使用 StorSimple 做為還原來源


從 StorSimple 還原的運作方式類似從任何區塊存放裝置還原。 還原已分層儲存到雲端的資料時，會以雲端速度進行還原。 如果是本機資料，則會以裝置的本機磁碟速度進行還原。 如需有關如何執行還原的資訊，請參閱 [NetBackup 文件(英文)](https://www.veritas.com/support/article.000094423)，並遵循合 NetBackup 還原最佳作法。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 容錯移轉和災害復原

> [!NOTE]
> 對於備份目標的案例，不支援使用 StorSimple Cloud Appliance 做為還原目標。

災害可能會因為各種因素而發生。 下表列出常見的災害復原 (DR) 案例。

| 案例                                                                    | 影響                                             | 如何復原                                                                                                                                                                               | 注意事項                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple 設備故障                                                | 備份和還原作業將會中斷。 | 更換故障的設備，並執行 [StorSimple 容錯移轉和災害復原](storsimple-device-failover-disaster-recovery.md) | 設備復原之後如果需要執行還原，則會從雲端擷取完整的使用中資料集到新設備，這將會導致所有作業都以雲端速度進行。 這個編製索引及編製目錄的重新掃描程序可能會造成所有備份集都要進行掃描並從雲端層提取到本機設備層，而這可能會非常耗時。                                                 |
| NetBackup 伺服器故障                                              | 備份和還原作業會中斷。 | 重建備份伺服器，並執行資料庫還原                                                                                                                                       | DR 站台的 NetBackup 伺服器必須重新建立或還原。 資料庫必須還原至最新的點。 如果還原的 NetBackup 資料庫沒有與您最新的備份作業同步，就必須編製索引及編製目錄。 重新掃描索引和目錄的程序可能會造成所有備份集都要進行掃描並從雲端層提取到本機裝置層。 這會更耗費時間。 |
| 站台故障造成備份伺服器和 StorSimple 都遺失。 | 備份和還原作業會中斷。 | 先還原 StorSimple，然後再還原 NetBackup。                                                                                                                                          | 先還原 StorSimple，然後再還原 NetBackup。                                                                 如果在裝置復原之後必須執行還原，則會從雲端擷取完整的使用中資料集到新裝置。 因此，所有作業都會以雲端速度進行。|

## <a name="references"></a>參考

本文中參考下列文件︰

- [StorSimple MPIO 設定](storsimple-configure-mpio-windows-server.md)

- [儲存體案例︰精簡佈建 (英文)](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [使用 GPT 磁碟機 (英文)](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [啟用與設定共用資料夾的陰影複製](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>後續步驟

深入了解：

- 了解如何[從備份集還原](storsimple-restore-from-backup-set-u2.md)。
- 了解如何執行[裝置容錯移轉和災害復原](storsimple-device-failover-disaster-recovery.md)。



<!--HONumber=Dec16_HO2-->


