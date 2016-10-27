<properties 
   pageTitle="將 VMWare 虛擬機器和實體伺服器容錯回復至內部部署網站 | Microsoft Azure"
   description="了解在將 VMware VM 和實體伺服器容錯移轉至 Azure 後，如何將其容錯回復到內部部署網站。" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/05/2016"
   ms.author="ruturajd"/>


# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>將 VMWare 虛擬機器和實體伺服器容錯回復至內部部署網站

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-failback-azure-to-vmware.md)
- [Azure 傳統入口網站](site-recovery-failback-azure-to-vmware-classic.md)
- [Azure 傳統入口網站 (舊版)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


本文說明如何將 Azure 虛擬機器從 Azure 容錯回復至內部部署網站。 當您準備好使用本 [教學課程](site-recovery-vmware-to-azure-classic.md)，將已從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請遵循本文中的指示。



## <a name="overview"></a>Overview

下圖說明此案例的容錯回復架構。

當處理序伺服器是內部部署，而且您使用 ExpressRoute，請使用此架構。

![Expressroute 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

當處理序伺服器在 Azure 上，而且您有 VPN 或 ExpressRoute 連接，請使用此架構。

![VPN 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

若要查看完整的連接埠清單及容錯回復架構圖，請參閱下圖

![容錯移轉-容錯回復所有的連接埠](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

容錯回復的運作方式如下：

- 在容錯移轉至 Azure 之後，您可以透過幾個階段來容錯回復至內部部署網站：
    - **階段 1**：重新保護 Azure VM，使其開始複寫回在內部部署網站中執行的 VMware VM。 
    - **階段 2**：Azure VM 複寫到內部部署網站後，就要執行容錯移轉以從 Azure 容錯回復。
    - **階段 3**：容錯回復資料之後，您必須重新保護已容錯回復到的內部部署 VM，使其開始複寫至 Azure。


### <a name="failback-to-the-original-or-alternate-location"></a>容錯回復到原始位置或替代位置

如果您已容錯移轉 VMware VM，在其仍存在於內部部署環境的前提下，您可以將其容錯回復到相同的來源 VM。 在此案例中，只會容錯回復差異變更。 請注意：

- 如果您容錯移轉實體伺服器，則一律會容錯回復至新的 VMware VM。
    - 在容錯回復實體機器之前，請注意：
        - 當從 Azure 容錯移轉回復至 VMware 時，受保護的實體機器會回復成虛擬機器。
        - 請確認您連同需要容錯回復的必要 ESX/ESXi 主機，有探索至少一部主要目標伺服器。
- 如果您要容錯回復到原始 VM，則必須符合下列條件：
    - 如果 VM 是由 vCenter 伺服器管理，主要目標的 ESX 主機應該可以存取 VM 資料存放區。
    - 如果 VM 位於 ESX 主機上，但不受 vCenter 管理，則 VM 的硬碟必須位於可由主要目標主機存取的資料存放區內。
    - 如果 VM 位於 ESX 主機上，但未使用 vCenter，則應該先完成主要目標 ESX 主機的探索後，才能重新保護。 如果您要容錯回復實體伺服器，則也適用這一條件。
    - 另一個選項 (如果內部部署 VM 存在) 是先將其刪除，然後才執行容錯回復。 如此一來，容錯回復便會在與主要目標 ESX 主機相同的主機上建立新的 VM。
    
- 當您容錯回復至替代位置時，資料將會復原到內部部署主要目標伺服器所使用的相同資料存放區和相同 ESX 主機上。 


## <a name="prerequisites"></a>必要條件

- 您需要擁有 VMware 環境才能容錯回復 VMware VM 和實體伺服器。 不支援容錯回復至實體伺服器。
- 若要容錯回復，您應該在最初設定保護時就已建立 Azure 網路。 要進行容錯回復，就需要有從 Azure 網路 (Azure VM 所在網路) 連往內部部署網站的 VPN 或 ExpressRoute 連線。
- 如果您想要容錯回復到的目標 VM 是由 vCenter 伺服器進行管理，您必須確定已擁有在 vCenter 伺服器上探索 VM 的必要權限。 [閱讀更多資訊](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
- 如果 VM 上有快照，重新保護程序將會失敗。 您可以刪除快照或磁碟。 
- 在容錯回復之前，您需要先建立幾個元件：
    - **在 Azure 中建立處理序伺服器**。 在容錯回復期間，您必須建立這個 Azure VM 並使其保持運作。 容錯回復完成後，便可以刪除該機器。
    - **建立主要目標伺服器**：主要目標伺服器會傳送及接收容錯回復資料。 您在內部部署環境中建立的管理伺服器預設會安裝主要目標伺服器。 不過，您可能必須根據容錯回復流量的大小，另外建立一個用來容錯回復的主要目標伺服器。
    - 如果您想要另外建立一個在 Linux 上執行的主要目標伺服器，您必須先設定 Linux VM，然後才安裝主要目標伺服器，詳情如下所述。
- 執行容錯回復時，組態伺服器必須為內部部署。 容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中，而失敗的容錯回復將不會成功。 因此，請確定您定期排定備份伺服器。 發生災害時，您需要使用相同的 IP 位址進行還原，讓容錯回復運作。
- 確定您在 VMware 中將主要目標 VM 的組態參數設定設為 disk.enableUUID=true。 如果該資料列不存在，請新增一個。 這樣才能提供一致的 UUID 給 VMDK，使其可正確掛接。
- **主要目標伺服器的儲存空間不可被 vMotion**。 這會造成容錯回復失敗。 VM 將不會啟動，因為磁碟將不供其使用。

## <a name="failback-policy"></a>容錯回復原則
若要複寫回內部部署，您需要容錯回復原則。 當您建立正向原則時，會自動建立此原則。 請注意 

1. 此原則於建立期間會自動與設定伺服器產生關聯。
2. 此原則不能編輯。
3. 原則的設定值為 (RPO 閾值 = 15 分鐘，復原點保留 = 24 小時，應用程式一致性快照頻率 = 60 分鐘) ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-the-process-server-in-azure"></a>在 Azure 中設定處理序伺服器

您必須在 Azure 中安裝處理序伺服器，讓 Azure VM 可以將資料傳回內部部署的主要目標伺服器。 

如果您已經將您的機器做為傳統資源保護 (也就是在 Azure 中復原的 VM 是傳統的 VM)，那麼您需要 Azure 中的傳統處理序伺服器。 如果您已經將機器復原為資源管理員部署類型，則您需要資源管理員部署類型的處理序伺服器。 類型是由您在其中部署處理序伺服器的 Azure 虛擬網路所選取。

1.  在 [保存庫] > [設定] > [站台復原基礎結構] (位於 [管理] 標題下方) > [組態伺服器] (位於 [適用於 VMware 與實體機器] 標題下方) 中，選取組態伺服器。 按一下於下列範例螢幕擷取畫面中以黃色醒目提示的 [+ 處理序伺服器]。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)

2. 選擇將處理序伺服器部署為「在 Azure 中部署容錯回復處理序伺服器」。

3. 選取您已在其中復原機器的訂用帳戶。 

4. 接下來選取您在其中擁有已復原機器的 Azure 網路。 處理序伺服器必須位於相同的網路中，復原的 VM 與處理序伺服器才能夠通訊。

5. 如果您已選取 [傳統部署]  網路 - 系統會要求您透過 Azure 資源庫建立新的 VM，並在其中安裝處理序伺服器。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)
    
    1. 映像的名稱是 Microsoft Azure Site Recovery Process Server V2 。 請確定您選取 [傳統]  做為部署模型。
    
        ![](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
    
    2. 根據 [這裡提供](./site-recovery-vmware-to-azure-classicz.md#step-5-install-the-management-server)
    
6. 如果您選取 [Resource Manager]  Azure 網路，您將需要提供下列的輸入來部署伺服器。

    1. 您想要部署伺服器的的資源群組
    
    2. 指定伺服器的名稱
    
    3. 指定使用者名稱與密碼，讓您可以登入
    
    4. 選擇您想要部署伺服器的儲存體帳戶
    
    5. 選擇特定子網路和網路介面來連接至處理序伺服器。 注意 - 您必須建立您自己的 [網路介面](../virtual-network/virtual-networks-multiple-nics.md) (NIC)，並在部署時選取它。
    
        ![](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)
    
    6. 按一下 [確定]。 這樣會觸發利用處理序伺服器安裝程式建立資源管理員部署類型虛擬機器的工作。 您必須在 VM 內部執行安裝程式，將伺服器註冊到組態伺服器。 您可以依照 [這些步驟](./site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)進行。

    7. 將觸發部署處理序伺服器的作業

7. 在結束時，處理序伺服器應該會列在 [組態伺服器] 頁面 [處理序伺服器] 索引標籤中的相關聯伺服器區段底下。
    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

        
    >[AZURE.NOTE] 該伺服器不會顯示在 [VM 屬性] 下。　 該伺服器只會顯示在它已註冊之管理伺服器的 [伺服器]  索引標籤底下。 處理序伺服器大約需要 10-15 分鐘才會出現。


## <a name="set-up-the-master-target-server-on-premises"></a>在內部部署環境中設定主要目標伺服器

主要目標伺服器會接收容錯回復資料。 主要目標伺服器會自動安裝在內部部署管理伺服器，但如果要容錯回復大量資料，則可能需要另外設定一個主要目標伺服器。 以下列方式來執行此動作：

>[AZURE.NOTE] 如果您想要在 Linux 上安裝主要目標伺服器，請遵循下一個程序中的指示。

1. 如果您要在 Windows 上安裝主要目標伺服器，請從要安裝主要目標伺服器的 VM 開啟 [快速啟動] 頁面，並下載 Azure Site Recovery 整合安裝精靈的安裝檔案。
2. 執行安裝程式，並在 [開始之前] 中選取 [新增額外處理序伺服器以相應放大部署]。
3. 以您 [設定管理伺服器](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)時的相同方式完成精靈。 在 [組態伺服器詳細資料]  頁面上，指定這個主要目標伺服器的 IP 位址和複雜密碼來存取 VM。

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>將 Linux VM 設定為主要目標伺服器
若要將執行主要目標伺服器的管理伺服器設定為 Linux VM，您必須安裝 CentOS 6.6 最小作業系統、擷取每個 SCSI 硬碟的 SCSI 識別碼、額外安裝某些封裝，並套用一些自訂變更。

#### <a name="install-centos-6.6"></a>安裝 CentOS 6.6

1.  在管理伺服器 VM 上安裝 CentOS 6.6 最小作業系統。 在 DVD 光碟機中放入 ISO，並啟動系統。 略過媒體測試，於語言選取美式英文，選取 [基本存放裝置]確認硬碟機上沒有重要資料，然後按一下 [是] 以捨棄其中的所有資料。 輸入管理伺服器的主機名稱，然後選取伺服器的網路介面卡。  在 [編輯系統] 對話方塊中，選取 [自動連線]，然後新增靜態 IP 位址、網路和 DNS 設定。 指定時區，以及用來存取管理伺服器的根密碼。 
2.  當系統要求您指定安裝類型時，請選取 [建立自訂配置] 做為磁碟分割。 按一下 [下一步] 之後，請選取 [免費] 並按一下 [建立]。 使用 [FS 類型：ext4] 來建立 **/**、**/var/crash** 和 **/home** 磁碟分割。 將交換磁碟分割建立為 [FS 類型：swap]。
3.  如果找到現存裝置，將會出現警告訊息。 按一下 [格式化]  以使用磁碟分割設定將該磁碟機格式化。 按一下 [將變更寫入磁碟]  以套用磁碟分割變更。
4.  選取 [安裝開機載入器]  >  [下一步] 以在根磁碟分割上安裝開機載入器。
5.  安裝完成時，按一下 [重新開機] 。


#### <a name="retrieve-the-scsi-ids"></a>擷取 SCSI 識別碼

1. 在安裝後，擷取 VM 中每個 SCSI 硬碟的 SCSI 識別碼。 若要執行此作業，請將管理伺服器 VM 關機，然後在 VMware 的 VM 屬性中以滑鼠右鍵按一下 VM 項目 > [編輯設定]  >  [選項]。
2. 選取 [進階]  >  [一般項目]，然後按一下 [組態參數]。 機器在執行時會停用此選項。 若要啟用，則必須將機器關閉。
3. 如果 **disk.EnableUUID** 資料列存在，請確定其值已設定為 [True] (有區分大小寫)。 如果已如此設定，則可加以取消，然後在機器開機之後，於客體作業系統內部測試 SCSI 命令。 
4. 如果該資料列不存在，請按一下 [加入資料列]，並以 [True] 值新增它。 請勿使用雙引號。

#### <a name="install-additional-packages"></a>安裝其他封裝

您必須另外下載並安裝一些封裝。 

1.  確定主要目標伺服器已連線到網際網路。
2.  執行下列命令以從 CentOS 儲存機制下載並安裝 15 個套件： **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**。
3.  如果所要保護之來源機器的根目錄或開機裝置是執行搭配 Reiser 的 Linux，或是 XFS 檔案系統，則應該下載並安裝下列額外封裝：

    - # <a name="cd-/usr/local"></a>cd /usr/local
    - # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-–ivh-kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm-reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-[http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-–ivh-xfsprogs-3.1.1-16.el6.x86_64.rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>套用自訂變更

完成後續安裝步驟並已安裝封裝之後，請執行下列動作以套用自訂變更：

1.  將 RHEL 6-64 整合代理程式二進位檔複製到 VM。 執行下列命令來解壓縮二進位檔：**tar –zxvf <file name>**
2.  執行此命令來授與權限： **# chmod 755 ./ApplyCustomChanges.sh**
3.  執行指令碼： **# ./ApplyCustomChanges.sh**。 指令碼只應執行一次。 在指令碼順利執行後，請重新啟動伺服器。


## <a name="run-the-failback"></a>執行容錯回復

### <a name="reprotect-the-azure-vms"></a>重新保護 Azure VM

1.  在 [保存庫] > [複寫項目] > 選取容錯移轉的 VM，並以滑鼠右鍵按一下 [重新保護]。 您也可以按一下機器，並從命令按鈕中選取重新保護。
2.  在刀鋒視窗中，您可以看到已選取保護方向 [Azure 至內部部署]。
3.  在 [主要目標伺服器] 和 [處理序伺服器] 中，選取內部部署主要目標伺服器和 Azure VM 處理序伺服器。
4.  選取您想要將內部部署磁碟復原至該位置的 [資料存放區]  。 當刪除內部部署 VM 且需要建立新的磁碟時會使用此選項。 如果磁碟已存在，但您仍然需要指定值的話，則會忽略此選項。 
5.  「保留磁碟機」是在將 VM 複寫回內部部署時，用來停止時間點。 保留磁碟機的某些準則如下，如果沒有這些準則，磁碟機將不會針對主要目標伺服器列出。
    a. 磁碟區不能使用於其他用途 (做為複寫目標等)。b. 磁碟區不能處於鎖定模式。
    c. 磁碟區不能是快取磁碟區。 (該磁碟區上不能存在 MT 安裝。 PS+MT 自訂安裝磁碟區不適合做為保留磁碟區。 這裡已安裝的 PS+MT 磁碟區是 MT 的快取磁碟區)。d. 磁碟區檔案系統類型不能是 FAT 和 FAT32。
    e. 磁碟區容量不能為零。
    e. Windows 的預設保留磁碟區為 R 磁碟區。 
    f. Linux 的預設保留磁碟區為 /mnt/retention。

6.  容錯回復原則將會自動選取。

7.  在您按一下 [確定]  以開始重新保護之後，就會開始執行將 VM 從 Azure 複寫到內部部署網站的作業。 您可以在 [作業]  索引標籤上追蹤進度。

如果您想要復原到替代位置，請選取為主要目標伺服器所設定的保留磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware VM 會使用與主要目標伺服器相同的資料存放區。 如果您想要將複本 Azure VM 復原到相同的內部部署 VM，則內部部署 VM 必須已位於與主要目標伺服器相同的資料存放區內。 如果內部部署環境中沒有任何 VM，則會在重新保護期間建立一個新的 VM。
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)



您也可以在復原計劃層級重新保護。 如果您有一個複寫群組，則只能使用復原計畫來重新保護它。 透過復原計劃重新保護時，您必須針對每一部受保護的機器提供上述的值。 

>[AZURE.NOTE] 複寫群組應該使用相同的主要目標重新保護。 如果使用不同的主要目標伺服器重新保護，則無法針對它採用一般時間點。

### <a name="run-a-failover-to-the-on-premises-site"></a>執行容錯移轉至內部部署網站

重新保護 VM 後，您可以起始從 Azure 至內部部署的容錯移轉。 

1.  在 [複製的項目] 頁面中，請選取虛擬機器，並以滑鼠右鍵按一下以選取 [非計劃性容錯移轉] 。
2.  在 [確認容錯移轉]  中，確認容錯移轉方向 (以 Azure 為來源)，並選取您想要用來進行容錯移轉的復原點 (最近的復原點或最近的應用程式一致復原點)。 應用程式一致復原點會在最近的復原點之後，因此將造成部分資料遺失。
3.  在容錯移轉期間，Site Recovery 會關閉 Azure VM。 在確認容錯回復已如預期完成後，您可以確認 Azure VM 是否已如預期般關閉。

### <a name="reprotect-the-on-premises-site"></a>重新保護內部部署網站

容錯回復完成之後，您必須認可虛擬機器，以確保會刪除在 Azure 中復原的 VM。

1. 在受保護的項目上按一下滑鼠右鍵，然後按一下 [認可]。 這將會觸發一個移除先前在 Azure 中已復原之虛擬機器的工作。

在認可完成後，內部部署網站上的資料會恢復，但不會受到保護。 若要再次啟動複寫至 Azure，請執行下列動作：

1.  在 [保存庫] > [設定] > [複寫項目] 中，選取具有容錯回復的 VM，按一下 [重新保護]。 
2.  提供必須用來將資料傳送回 Azure 之處理序伺服器的值。
3.  按一下 [確定] 開始重新保護工作。

重新保護完成之後，VM 會複寫回 Azure，您就可以執行容錯移轉。


### <a name="common-issues-in-failback"></a>容錯回復的常見問題

1. 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，這樣會成功且容錯回復可作用。 在重新保護的時候則會失敗，因為探索不到資料存放區。 徵兆是重新保護的時候不會列出資料存放區。 若要解決此問題，您可以使用有適當權限的帳戶更新 vCenter 認證並重試作業。 [閱讀更多資訊](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. 當您將 Linux VM 容錯回復並在內部部署執行它時，您會看到 Network Manager 封裝已從該機器解除安裝。 這是因為 VM 在 Azure 中復原時，Network Manager 封裝遭移除。
3. 當 VM 是以靜態 IP 位址設定且容錯移轉至 Azure 時，IP 位址則是透過 DHCP 取得。 當您容錯移轉至內部時，該 VM 會繼續使用 DHCP 取得 IP 位址。 如有需要，則您必須手動登入該機器並將 IP 位址設為原本的靜態位址。
4. 如果您是使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 您將需要升級為評估授權才能啟用容錯回復。

## <a name="failing-back-with-expressroute"></a>透過 ExpressRoute 容錯回復

您可以透過 VPN 連線或 Azure ExpressRoute 進行容錯回復。 如果您想要使用 ExpressRoute，請注意下列事項：

- ExpressRoute 應該設定在來源機器所要容錯移轉到的 Azure 虛擬網路上，以及容錯移轉發生時 Azure VM 所位於的 Azure 虛擬網路上。
- 資料會複寫至公用端點上的 Azure 儲存體帳戶。 您應該在 ExpressRoute 中設定與目標資料中心的公用對等互連，這樣一來，Site Recovery 複寫才能使用 ExpressRoute。






<!--HONumber=Oct16_HO2-->


