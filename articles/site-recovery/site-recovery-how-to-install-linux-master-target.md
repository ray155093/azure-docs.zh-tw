---
title: "如何安裝 Linux 主要目標伺服器以從 Azure 容錯移轉至內部部署 | Microsoft Docs"
description: "在重新保護 Linux 虛擬機器之前，您必須先有 Linux 主要目標伺服器。 了解如何進行安裝。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 60102ebb43efc8710f102450df5b98edcb1d4b39
ms.contentlocale: zh-tw
ms.lasthandoff: 05/08/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>如何安裝 Linux 主要目標伺服器
您可以在容錯移轉虛擬機器之後，將虛擬機器容錯回復至內部部署網站。 若要進行容錯回復，您需要在從 Azure 到內部部署網站的過程中重新保護虛擬機器。 針對此程序，您需要內部部署的主要目標伺服器以接收流量。 如果受保護的虛擬機器是 Windows 虛擬機器，您需要 Windows 主要目標。 若是 Linux 虛擬機器，您需要 Linux 主要目標。 請參閱下列步驟，以了解如何建立和安裝 Linux 主要目標。

## <a name="overview"></a>概觀
本文提供安裝 Linux 主要目標的資訊和指示。

在本文末尾或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中張貼意見或問題。

## <a name="prerequisites"></a>必要條件

* 若要正確選擇需要在其中部署主要目標的主機，請確定容錯回復的目的地是現有內部部署虛擬機器還是新的虛擬機器 (因為內部部署虛擬機器會遭到刪除)。
    * 若是現有虛擬機器，主要目標的主機應該要能存取虛擬機器的資料存放區。
    * 如果內部部署虛擬機器不存在，容錯回復虛擬機器會建立在作為主要目標的相同主機上。 您可以選擇任何 ESXi 主機來安裝主要目標。
* 主要目標應位於可與處理序伺服器及組態伺服器通訊的網路上。
* 主要目標的版本必須等於或早於處理序伺服器和組態伺服器的版本。 例如，若組態伺服器的版本是 9.4，則主要目標的版本可以是 9.4 或 9.3，但不能是 9.5。
* 主要目標只能是 VMware 虛擬機器，不能是實體伺服器。

## <a name="master-target-sizing-guideline"></a>主要目標調整大小方針

主要目標需要透過下列調整大小方針來建立
    * 記憶體：6GB 或更多
    * OS 磁碟大小：100GB 或更多 (以安裝 CentOS6.6)
    * 用於保留磁碟機的額外磁碟大小：1TB
    * CPU 核心：4 核心或更多


## <a name="steps-to-deploy-the-master-target-server"></a>主要目標伺服器的部署步驟

### <a name="install-centos-66-minimal"></a>安裝 CentOS 6.6 minimal

使用下列步驟以安裝 64 位元 CentOS 6.6 作業系統︰

1. 從下列連結選擇最近的鏡像來下載 CentOS 6.6 minimal 64 位元 ISO。

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    將 CentOS 6.6 minimal 64 位元 ISO 放在 DVD 光碟機中，並啟動系統。

    ![[Welcome to CentoOS 6.6] 對話方塊](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. 按一下 [Skip] 來忽略媒體測試程序。

    ![選取 [Skip] 來忽略媒體測試程序](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. 在安裝歡迎畫面上按一下 [Next] 按鈕。

    ![安裝歡迎畫面上的 [Next] 按鈕](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 選取 [English] 作為慣用語言，然後按一下 [Next]。

    ![選取語言](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. 選取 [US English] 作為鍵盤配置，然後按一下 [Next]。

    ![選取 [English] 鍵盤配置](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. 選取 [Basic storage Devices]，然後按一下 [Next]。

    ![選取存放裝置](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. 出現的警告訊息指出將會刪除硬碟中的現有資料。 請確定硬碟內沒有任何重要資料，然後按一下 [Yes, discard any data]。

    ![若繼續將會刪除資料的相關警告](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. 在 [Hostname] 方塊中輸入伺服器的主機名稱，然後按一下 [Configure Network]。 在 [Network Connection] 對話方塊中選取您的網路介面，然後按一下 [Edit] 按鈕以進行 IPV4 設定。

    ![選取主機名稱並設定 IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. 在 [Editing System eth0] 對話方塊中核取 [Connect automatically] 核取方塊。 在 [IPv4 Settings] 索引標籤上，針對 [Method] 選擇 [Manual]，然後按一下 [Add] 按鈕。 提供 [Static IP]、[Netmask]、[Gateway] 和 [DNS 伺服器] 的詳細資料。 按一下 [Apply] 儲存詳細資料。

    ![網路組態的設定](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. 選取時區，然後按一下 [Next]。

    ![選取時區](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. 輸入 [Root Password]、確認密碼，然後按一下 [Next]。

    ![新增密碼](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. 選取 [Create Custom Layout]，然後按一下 [Next]。

    ![選取安裝類型](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. 選取 [Free] 資料分割，然後按一下 [Create] 以建立 **/**、**/var/crash** 和 **/home** 資料分割，並以 **ext4** 作為檔案系統類型。 以 **swap** 作為檔案系統類型來建立**交換資料分割**。 若要配置資料分割大小，請遵循下表中的大小配置公式。

    > [!NOTE]
    > Linux 主要目標伺服器不應針對根或保留儲存空間使用邏輯磁碟區管理員 (LVM)。 預設會設定 Linux 主要目標，以避免 LVM 資料分割和磁碟探索。

    ![資料分割名稱、資料分割大小和檔案系統類型的資料表](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. 建立資料分割之後，按一下 [Next]。

    ![顯示所選資料分割值的對話方塊](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 如果找到任何早已存在的裝置，就會出現格式化的警告訊息。 按一下 [Format] 以最新的資料分割資料表將硬碟格式化。

    ![按一下 [Format] 按鈕以將磁碟格式化](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. 按一下 [Write changes to disk]，對磁碟套用資料分割變更。

    ![按一下 [Write changes to disk]](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. 勾選 [Install boot loader] 選項，然後按一下 [Next] 將開機載入器安裝在根資料分割上。

    ![將開機載入器安裝在根資料分割上](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. 安裝程序隨即開始。 您可以監視進度。

    ![顯示安裝進度的對話方塊](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. 下列畫面會顯示安裝已成功完成。 按一下 [Reboot]。

    ![安裝成功畫面](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>後續安裝步驟
接下來，準備要設定為主要目標伺服器的機器。

若要取得 Linux 虛擬機器中每個 SCSI 硬碟的識別碼，您應該啟用 **disk.EnableUUID = TRUE** 參數。

若要啟用此參數，請使用下列步驟︰

1. 關閉虛擬機器。

2. 以滑鼠右鍵按一下左窗格中的虛擬機器項目，然後選取 [Edit Settings]。

3. 按一下 [選項]  索引標籤。

4. 在左窗格中選取 [進階] &gt; [一般]，然後按一下右邊的 [組態參數] 按鈕。

    ![[選項] 索引標籤](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    [組態參數] 選項在機器執行時無法使用。 若要啟用此索引標籤，請關閉虛擬機器。

5. 查看含有 [disk.EnableUUID] 的資料列是否已經存在。

    - 如果該值存在，而且設定為 [False]，請將該值變更為 [True] \(值不區分大小寫)。

    - 如果該值存在，而且設定為 [True]，請按一下 [Cancel]。

    - 如果該值不存在，請按一下 [新增資料列]。

    - 在 [Name] 資料行中新增 [disk.EnableUUID]，並將其值設為 [TRUE]。

    ![檢查 disk.EnableUUID 是否已存在](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>下載並安裝其他套件

> [!NOTE]
> 請確定您有網際網路連線以便下載並安裝其他套件。 若沒有網際網路連線，則必須以手動方式尋找這些 RPM 套件並加以安裝。

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

前面的命令會從 CentOS 6.6 儲存機制下載以下 15 個套件並進行安裝。 如果您無法存取網際網路，則必須下載下列 RPM 套件：


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>針對特定作業系統安裝其他套件

> [!NOTE]
> 如果來源所保護的機器針對根目錄或開機裝置使用 ReiserFS 或 XFS 檔案系統，請下載以下額外套件並將其安裝在 Linux 主要目標上，才能提供保護。


ReiserFS (如果在 Suse11SP3 使用。ReiserFS 不是 Suse11SP3 中的預設檔案系統)

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

XFS (RHEL、CentOS 7 (含) 更新版本)

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath
```
這是在主要目標上啟用多重路徑套件的必要做法。

### <a name="get-the-installer-for-setup"></a>取得安裝程式

如果您的主要目標有網際網路連線，您可以使用下列步驟下載安裝程式。 否則，您可以從處理序伺服器複製安裝程式並加以安裝。

#### <a name="download-the-master-target-installation-packages"></a>下載主要目標安裝套件

[下載最新的 Linux 主要目標安裝位元](https://aka.ms/latestlinuxmobsvc)。

若要使用 Linux 下載它，請輸入：

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

請務必在主目錄中下載並解壓縮安裝程式。 如果您解壓縮至 /usr/Local，安裝將會失敗。


#### <a name="access-the-installer-from-the-process-server"></a>從處理序伺服器存取安裝程式

1. 移至處理序伺服器上的 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository。

2. 從處理序伺服器複製必要的安裝程式檔案，在主目錄中將它儲存為 latestlinuxmobsvc.tar.gz。


### <a name="apply-custom-configuration-changes"></a>套用自訂組態變更

若要套用自訂組態變更，請使用下列步驟︰


1. 執行下列命令來解壓縮二進位檔。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![執行命令的螢幕擷取畫面](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 執行下列命令來授與權限。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 執行下列命令來執行指令碼。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 只需在伺服器上執行指令碼一次。 關閉伺服器。 如後續步驟所述新增磁碟後，重新啟動伺服器。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>將保留磁碟新增至 Linux 主要目標虛擬機器

使用下列步驟來建立保留磁碟：

1. 將新的 **1-TB** 磁碟連結至 Linux 主要目標虛擬機器，然後將機器**開機**。

2. 使用 **multipath -ll** 命令以便得知保留磁碟的多重路徑識別碼。

    ```
    multipath -ll
    ```

    ![保留磁碟的多重路徑識別碼](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 格式化磁碟機，並在新的磁碟機上建立檔案系統。

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![在磁碟機上建立檔案系統](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 建立檔案系統之後，掛接保留磁碟。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![掛接保留磁碟](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 建立 **fstab** 項目，以便在每次開機時掛接保留磁碟機。
    ```
    vi /etc/fstab
    ```
    按 **Insert** 鍵來開始編輯檔案。 建立新的一行，並插入下列文字。 根據前一個命令中醒目提示的多重路徑識別碼，編輯磁碟多重路徑識別碼。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    按 **Esc** 鍵，然後輸入 **:wq** (寫入和結束)，以關閉編輯器視窗。

### <a name="install-the-master-target"></a>安裝主要目標

> [!IMPORTANT]
> 主要目標伺服器的版本必須等於或早於處理序伺服器和組態伺服器的版本。 如果不符合此條件，重新保護將會成功，但複寫會失敗。


> [!NOTE]
> 在安裝主要目標伺服器之前，請確認虛擬機器上的 /etc/hosts 檔案包含會將本機主機名稱對應到所有網路介面卡相關 IP 位址的項目。

1. 從組態伺服器的 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 複製複雜密碼，然後執行下列命令，將它儲存在相同本機目錄的 passphrase.txt 中。

    ```
    echo <passphrase> >passphrase.txt
    ```
    範例：echo itUx70I47uxDuUVY >passphrase.txt

2. 記下組態伺服器的 IP 位址。 您在下一步需要用到它。

3. 執行下列命令來安裝主要目標伺服器，並向組態伺服器註冊伺服器。

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    範例：./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    等候指令碼完成。 如果主要目標註冊成功，主要目標會列在入口網站的 [Site Recovery 基礎結構] 頁面上。

#### <a name="install-the-master-target-by-using-interactive-install"></a>使用互動式安裝來安裝主要目標

1. 執行下列命令來安裝主要目標。 選擇 [Master Target] 作為代理程式角色。

    ```
    ./install
    ```

2. 選擇預設安裝位置，然後按 **Enter** 鍵以繼續。

    ![選擇主要目標的預設安裝位置](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. 選擇 [Global] 設定來進行設定。

    ![設定 Global 設定](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. 指定組態伺服器的 IP 位址。

5. 將組態伺服器的連接埠指定為 443。

    ![指定組態伺服器的 IP 位址和連接埠](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. 從組態伺服器的 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 複製組態伺服器複雜密碼，並將它貼到 [Passphrase] 方塊中。 即使在貼上文字後，該方塊仍會是空白的。

7. 移至功能表中的 [Quit]。

8. 讓安裝和註冊完成。

### <a name="install-vmware-tools-on-the-master-target-server"></a>在主要目標伺服器上安裝 VMware 工具

您必須將 VMware 工具安裝在主要目標上，以便它可以探索資料存放區。 如果未安裝工具，重新保護畫面就不會列出資料存放區。 安裝 VMware 工具之後，您必須重新開機。

## <a name="next-steps"></a>後續步驟
在主要目標完成安裝和註冊之後，您可以看到主要目標出現在 [Site Recovery 基礎結構] 中的 [主要目標] 區段 (在組態伺服器概觀下)。

您現在可以繼續[重新保護](site-recovery-how-to-reprotect.md)，接著進行容錯回復。

## <a name="common-issues"></a>常見問題

* 請確定您未在任何管理元件 (例如主要目標) 上啟動 Storage vMotion。 如果在成功重新保護之後主要目標有移動，則無法中斷連結虛擬機器磁碟 (VMDK)，而且容錯回復會失敗。
* 主要目標在虛擬機器上不應該有任何快照集。 如果有快照集，容錯回復會失敗。
* 由於某些客戶會有一些自訂 NIC 組態，因此啟動期間會停用網路介面，導致主要目標代理程式無法初始化。 請確定已正確設定下列屬性。 在乙太網路卡檔案的 /etc/sysconfig/network-scripts/ifcfg-eth* 中檢查這些屬性。
    * BOOTPROTO=dhcp
    * ONBOOT=yes

