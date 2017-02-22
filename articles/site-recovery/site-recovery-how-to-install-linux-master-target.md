---
title: "如何安裝 Linux 主要目標伺服器 | Microsoft Docs"
description: "在重新保護 Linux VM 之前，您必須先有 Linux 主要目標伺服器。 了解如何進行安裝。"
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>如何安裝 Linux 主要目標伺服器
Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。 機器可以複寫至 Azure，或次要的內部部署資料中心。 如需快速概觀，請參閱 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

## <a name="overview"></a>概觀
本文提供復原 (容錯移轉和容錯回復) 使用 Site Recovery 保護的虛擬機器與實體伺服器的相關資訊和指示。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="pre-requisites"></a>必要條件

1. 若要正確選擇要在那個主機上部署 MT，請先確定容錯回復的目的地是現有的內部部署 VM 或新的 VM (因為內部部署 VM 會遭到刪除)。 
    * 若是現有 VM，MT 的主機應該要能存取 VM 的資料存放區。
    * 如果 VM 不是存在於內部部署環境，容錯回復 VM 會建立在做為 MT 的同一部主機上。
2. MT 應位於可與處理序伺服器及組態伺服器通訊的網路上。
3. MT 版本應小於或等於處理序伺服器及組態伺服器  (例如，如果 CS 位於 9.4 版，MT 可位於 9.4 和 9.3 版，但不能位於 9.5 版)
4. MT 只能是 VMware VM，而不能是實體 VM。


## <a name="steps-to-deploy-the-master-target-server"></a>主要目標伺服器的部署步驟

### <a name="install-centos-66-minimal"></a>安裝 CentOS 6.6 Minimal

請遵循下面所述步驟來安裝 CentOS 6.6 64 位元作業系統。

**步驟 1：**從下列連結選擇最近的鏡像來下載 CentOS 6.6 minimal 64 位元 ISO。

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

將 CentOS 6.6 minimal 64 位元 ISO 放在 DVD 光碟機中，並啟動系統。

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**步驟 2：**選取 [Skip] 來忽略媒體測試程序。

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**步驟 3：**現在您可以看到安裝歡迎畫面。 在此按一下 [Next] 按鈕。

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**步驟 4：**選取 [English] 做為慣用語言，然後按一下 [Next] 以繼續。

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**步驟 5：**選取 [US English] 做為鍵盤配置。 按一下 [Next] 繼續安裝。

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**步驟 6：**選取要安裝的裝置類型。 選取 [Basic storage Devices]。

按一下 [Next] 繼續安裝。

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**步驟 7：**此時會出現警告訊息，表示將會刪除硬碟中的現有資料。 請確定硬碟內沒有任何重要資料，然後按一下 [Yes, discard any data]。

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**步驟 8：**在 [Hostname] 文字方塊中輸入伺服器的主機名稱。
按一下 [Configure Network]。

在 [Network Connection] 視窗中選取網路介面。 按一下 [Edit] 按鈕來設定 IPV4 設定。

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**步驟 9：**此時會顯示下列 [Editing System eth0] 視窗。 選取 [Connect automatically] 核取方塊。 在 [IPv4 Settings] 索引標籤下，選擇 [Manual] 做為方法，然後按一下 [Add] 按鈕。 提供靜態 IP、網路遮罩、閘道和 DNS 伺服器的詳細資料。 按一下 [Apply] 儲存詳細資料。

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**步驟 10：**從下拉式方塊中選取您的時區，然後按一下 [Next] 以繼續。

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**步驟 11：**輸入 [Root password] 並確認密碼，然後按一下 [Next] 以繼續。

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**步驟 12：**選取 [Create Custom Layout] 做為資料分割模式，然後按一下 [Next] 以繼續。

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**步驟 13：**選取 [Free] 資料分割，然後按一下 [Create] 以建立 **/**、**/var/crash** 和 **/home** 資料分割，並以 **ext4** 做為檔案系統類型。 以 **swap** 做為檔案系統類型來建立**交換資料分割**。 若要配置資料分割大小，請遵循下表所述的大小配置公式。

注意：Linux 主要目標 (MT) 系統不應針對根或保留儲存空間使用 LVM。 預設會設定 Linux MT，以避免 LVM 磁碟分割/磁碟探索。

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**步驟 14：**在建立資料分割後，按一下 [Next] 以繼續。

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**步驟 15：**如果找到任何早已存在的裝置，就會出現格式化的警告訊息。

按一下 [Format] 以最新的資料分割資料表將硬碟格式化。

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**步驟 16：**按一下 [Write changes to disk] 對磁碟套用資料分割變更。

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**步驟 17：**核取 [Install boot loader] 選項，然後按一下 [Next] 將開機載入器安裝在根分割上。

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**步驟 18：**安裝程序隨即開始。 您可以監視安裝進度。

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**步驟 19：**下列畫面會顯示安裝已成功完成。 按一下 [Reboot]

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>後續安裝步驟

若要取得 Linux 虛擬機器中每個 SCSI 硬碟的 SCSI 識別碼，您應該啟用參數 “disk.EnableUUID = TRUE”。

若要啟用此參數，請依照以下指定的步驟執行：

a. 關閉虛擬機器。

b. 以滑鼠右鍵按一下左面板中的 VM 項目，然後選取 [編輯設定]。

c. 按一下 [選項]  索引標籤。

d. 選取左方的 [進階] &gt; [一般項目]，然後按一下右方顯示的 [組態參數]。

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

當機器正在執行時，[組態參數] 選項將處於停用狀態。 若要讓此索引標籤變成作用中狀態，請關閉機器。

e. 查看含有 **disk.EnableUUID** 的資料列是否已經存在？

  如果存在且已將值設為 False，即會使用 True 來覆寫值 (True 和 False 值是不區分大小寫的)。

  如果存在且已設為 True，可按一下 [取消]，然後在其開機之後，於客體作業系統內部測試 SCSI 命令。

f. 如果不存在，可按一下 [加入資料列]。

  在 [名稱] 欄中加入 disk.EnableUUID。

  將其值儲存為 TRUE

注意：請勿為上述值加上雙引號。

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>下載並安裝其他封裝

注意：請先確定系統具有網際網路連線，然後再下載並安裝其他封裝。

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

上述命令將從 CentOS 6.6 儲存機制下載如下所述的 15 個封裝並進行安裝。


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

注意：如果來源受保護機器會針對根目錄或開機裝置使用 Reiser 或 XFS 檔案系統，則下列其他套件應該下載並安裝於 Linux 主要目標上，才能提供保護。

ReiserFS (Suse11SP3。雖然 ReiserFS 不是 Suse11SP3 中的預設檔案系統)

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

XFS (RHEL、CentOS 7 (含) 更新版本)

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
這是在 MT 伺服器上啟用「多重路徑」套件的必要做法。

### <a name="download-the-mt-installation-packages"></a>下載 MT 安裝套件

從下列網址下載最新的 Linux 主要目標安裝位元 [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc)。

若要透過 Linux 下載它，請輸入 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

請務必只在主目錄中下載並解壓縮安裝程式。 如果您解壓縮至 /usr/Local，安裝將會失敗。

### <a name="apply-custom-configuration-changes"></a>套用自訂組態變更

套用自訂組態變更之前，請確定您已經完成後續安裝步驟

若要套用自訂組態變更，請依照如下所述的步驟執行：

1. 將 RHEL6-64 整合代理程式二進位檔複製到新建立的作業系統。

2. 執行下列命令來解壓縮二進位檔。
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. 執行下列命令來授與權限。
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. 執行下列命令來執行指令碼。
    ```
    # ./ApplyCustomChanges.sh
    ```
注意：只需在伺服器上執行指令碼一次。 順利執行上述指令碼之後，請將伺服器**重新開機**。

### <a name="add-retention-disk-to-linux-mt-vm"></a>將保留磁碟新增至 Linux MT VM 

請遵循如下所述步驟建立保留磁碟。

**步驟 1：**附加新的 **1 TB** 磁碟至 Linux MT VM，並找出其多重路徑識別碼

叫用 **multipath -ll** 命令以便得知保留磁碟的多重路徑識別碼。

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**步驟 2：**叫用 **mkfs.ext4 /dev/mapper/<保留磁碟的多重路徑識別碼>** 命令，在保留磁碟上建立檔案系統。

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**步驟 3：**檔案系統建立完成後，叫用下列命令以掛接保留磁碟。

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**步驟 4：**最後建立 fstab 項目︰
```
vi /etc/fstab 
```
並附加下列一行文字

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>安裝主要目標


> [!NOTE]
> 主要目標伺服器版本應小於或等於處理序伺服器及組態伺服器。 如果 MT 版本較高，重新保護將會成功，但複寫會失敗。
> 

> [!NOTE]
> 在安裝主要目標伺服器之前，請確認 VM 上的 /etc/hosts 檔案包含會將本機主機名稱對應到所有網路介面卡相關 IP 位址的項目。
> 


1. 執行下列命令來安裝主要目標。 選擇「主要目標」做為代理程式角色。
```
# ./install
```

2. 選擇預設安裝位置
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 選擇要設定的全域設定

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. 指定 CS 伺服器 IP 位址

5. 指定 CX 伺服器連接埠，通常是 9443。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 在組態伺服器上從 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 複製 CS 複雜密碼。

7. 讓安裝和註冊完成。
### <a name="install-vmware-tools-on-the-master-target-server"></a>在主要目標伺服器上安裝 VMware 工具

VMware 工具必須安裝在 MT 上，以便可以找出資料存放區。 如果未安裝工具，重新保護畫面就不會列出資料存放區。

## <a name="next-steps"></a>後續步驟
 

## <a name="common-issues"></a>常見問題



<!--HONumber=Feb17_HO3-->


