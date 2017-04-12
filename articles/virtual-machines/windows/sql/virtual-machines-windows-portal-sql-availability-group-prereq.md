---
title: "SQL Server 可用性群組 - Azure 虛擬機器 - 必要條件 | Microsoft Docs"
description: "本教學課程示範如何設定在 Azure VM 中建立 SQL Server Always On 可用性群組的必要條件。"
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 8073a2ed0b565b1fdd2685f9c0f69abf2a3fc10a
ms.lasthandoff: 03/31/2017


---

# <a name="complete-prerequisites-for-creating-always-on-availability-groups-in-azure-virtual-machines"></a>完成在 Azure 虛擬機器中建立 Always On 可用性群組的必要條件

本教學課程示範如何完成[在 Azure虛擬機器上建立 SQL Server Always On 可用性群組](virtual-machines-windows-portal-sql-availability-group-tutorial.md)的必要條件。 必要條件完成之後，您在單一資源群組中有網域控制站、兩個 SQL 伺服器和見證伺服器。

**估計時間**︰可能要花幾個小時才能完成必要條件。 這個時間大部分都是花在建立虛擬機器。

下圖說明您在本教學課程中建置的項目。

![可用性群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>檢閱可用性群組文件

本教學課程假設您對「SQL Server Always On 可用性群組」有基本的了解。 如果您不熟悉這項技術，請參閱 [Always On 可用性群組概觀 (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx)。


## <a name="create-an-azure-account"></a>建立 Azure 帳戶
* 您需要 Azure 帳戶。 您可以[申請免費 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)或[啟用 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 

## <a name="create-resource-group"></a>建立資源群組
1. 登入 [Azure 入口網站](http://portal.azure.com)。 
2. 按一下 + 在入口網站中建立新的物件。

   ![新增](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

1. 在 [Marketplace] 搜尋視窗中輸入**資源群組**。
   
   ![資源群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
1. 按一下 [資源群組]。
4. 按一下頁面底部的 [新增] 。 
5. 在 [資源群組] 刀鋒視窗的 [資源群組名稱] 類型之下，輸入資源群組的名稱。 例如，輸入 **sql-ha-rg**。
6. 如果您有多個 Azure 訂用帳戶，請確認此訂用帳戶是您要在其中建立可用性群組的 Azure 訂用帳戶。 
7. 選取位置。 此位置是您要建立可用性群組的 Azure 區域。 在本教學課程中，我們將在一個 Azure 位置中建置所有資源。 
8. 確認已核取 [釘選到儀表板]  。 這個選擇性設定會在 Azure 入口網站儀表板上放置資源群組的捷徑。

   ![資源群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

9. 按一下 [建立]  以建立資源群組。

Azure 會建立資源群組，並在入口網站中釘選資源群組的捷徑。

## <a name="create-network-and-subnets"></a>建立網路和子網路
下一個步驟是在 Azure 資源群組中建立網路和子網路。

此解決方案會使用具有兩個子網路的一個虛擬網路。 [Virtual Network Overview (虛擬網路概觀)](../../../virtual-network/virtual-networks-overview.md) 提供 Azure 中網路的詳細資訊。

若要建立虛擬網路：

1. 在 Azure 入口網站的資源群組中，按一下 [+ 新增]。 Azure 即會開啟 [所有項目]  刀鋒視窗。 
   
   ![新增項目](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. 搜尋 **虛擬網路**。
   
     ![搜尋虛擬網路](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. 按一下 [虛擬網路] 。
4. 在 [虛擬網路] 刀鋒視窗中，按一下 [Resource Manager] 部署模型，然後按一下 [建立]。
   
   下表顯示虛擬網路的設定：
   
   | **欄位** | 值 |
   | --- | --- |
   | **名稱** |autoHAVNET |
   | **位址空間** |10.33.0.0/24 |
   | **子網路名稱** |Admin |
   | **子網路位址範圍** |10.33.0.0/29 |
   | **訂用帳戶** |指定您打算使用的訂用帳戶。 如果您只有一個訂用帳戶，則**訂用帳戶**為留白。 |
   | **位置** |指定 Azure 位置。 |
   
   您的位址空間和子網路位址範圍可能與此表有所不同。 視您的訂用帳戶而定，入口網站會建議可用的位址空間和對應的子網路位址範圍。 如果沒有足夠的位址空間可供使用，請使用不同的訂用帳戶。 

   此範例會使用子網路名稱 **Admin**。 此子網路用於網域控制站。 

6. 按一下 [建立] 。
   
   ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure 會讓您回到入口網站儀表板，並在建立完新網路時通知您。

### <a name="create-a-second-subnet"></a>建立第二個子網路
新的虛擬網路有一個子網路，名為 **Admin**。 網域控制站會使用此子網路。 SQL Server 會使用名為 **SQL**的第二個子網路。 若要設定此子網路：

1. 在儀表板上，按一下您所建立的資源群組 [SQL-HA-RG] 。 在 [資源] 之下的資源群組中尋找此網路。
   
    如果看不到 [SQL-HA-RG]，請按一下 [資源群組] 並依資源群組名稱進行篩選，即可找到它。
2. 按一下資源清單上的 [autoHAVNET]  。 Azure 會開啟 [網路組態] 刀鋒視窗。
3. 在 **autoHAVNET** 虛擬網路上，按一下 [所有設定]。
4. 在 [設定] 刀鋒視窗上，按一下 [子網路]。
   
    請注意您已建立的子網路。 
   
   ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. 建立第二個子網路。 按一下 [+ 子網路] 。 
6. 在 [新增子網路] 刀鋒視窗中，於 [名稱] 之下輸入 **sqlsubnet** 來設定子網路。 Azure 會自動指定有效的 [位址範圍] 。 確認此位址範圍中至少有 10 個位址。 在生產環境中，您可能需要更多位址。 
7. 按一下 [確定] 。
   
    ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

下表摘要說明網路組態設定︰

| **欄位** | 值 |
| --- | --- |
| **名稱** |**autoHAVNET** |
| **位址空間** |取決於您訂用帳戶中的可用位址空間。 一般的值是 10.0.0.0/16。 |
| **子網路名稱** |**admin** |
| **子網路位址範圍** |取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.0.0/24。 |
| **子網路名稱** |**sqlsubnet** |
| **子網路位址範圍** |取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.1.0/24。 |
| **訂用帳戶** |指定您打算使用的訂用帳戶。 |
| **資源群組** |**SQL-HA-RG** |
| **位置** |指定您為資源群組選擇的相同位置。 |

## <a name="create-availability-sets"></a>建立可用性設定組

建立虛擬機器之前，您必須建立可用性設定組。 可用性設定組可縮短計劃性或非計劃性維護事件的停機時間。 Azure 可用性設定組是 Azure 置於實體容錯網域和更新網域上的邏輯資源群組。 容錯網域會確保可用性設定組的成員具有個別的電源和網路資源。 更新網域會確保可用性設定組的成員不會同時停機來進行維護。 [管理虛擬機器的可用性](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

您需要兩個可用性設定組。 一個用於網域控制站。 第二個用於 SQL Server。

若要建立可用性設定組，請移至資源群組，然後按一下 [新增]。 輸入**可用性設定組**以篩選結果。 按一下結果中的 [可用性設定組]。 按一下 [建立] 。

根據下表中的參數設定兩個可用性設定組：

| **欄位** | 網域控制站可用性設定組 | SQL Server 可用性設定組 |
| --- | --- | --- |
| **名稱** |adavailabilityset |sqlavailabilityset |
| **資源群組** |SQL-HA-RG |SQL-HA-RG |
| **容錯網域** |3 |3 |
| **更新網域** |5 |3 |

建立可用性設定組之後，請回到 Azure 入口網站中的資源群組。

## <a name="create-domain-controllers"></a>建立網域控制站
在您建立網路、子網路、可用性設定組及網際網路對向負載平衡器之後， 您已準備好建立網域控制站的虛擬機器。

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>建立網域控制站的虛擬機器
若要建立和設定網域控制站，請回到 **SQL-HA-RG** 資源群組。

1. 按一下 [新增]。 [所有項目]  刀鋒視窗隨即開啟。
2. 輸入 **Windows Server 2016 資料中心**。 
3. 按一下 **Windows Server 2016 資料中心**。 在 [Windows Server 2016 資料中心] 刀鋒視窗中，確認部署模型為 [Resource Manager]，然後按一下 [建立]。 Azure 會開啟 [建立虛擬機器]  刀鋒視窗。 

重複上述步驟建立兩個虛擬機器的步驟。 為兩部虛擬機器命名︰

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** 是選擇性元件，可為 Active Directory 網域服務提供高可用性。 
  > 
  > 

下表顯示這兩部機器的設定：

| **欄位** | 值 |
| --- | --- |
| **VM 磁碟類型** |SSD |
| **使用者名稱** |DomainAdmin |
| **密碼** |Contoso!0000 |
| **訂用帳戶** |*您的訂用帳戶* |
| **資源群組** |SQL-HA-RG |
| **位置** |*您的位置* |
| **大小** |DS1_V2 |
| **儲存體帳戶** |*自動建立* |
| **虛擬網路** |autoHAVNET |
| **子網路** |admin |
| **公用 IP 位址** |*與 VM 同名* |
| **網路安全性群組** |*與 VM 同名* |
| **可用性設定組** |adavailabilityset |
| **診斷** |已啟用 |
| **診斷儲存體帳戶** |*自動建立* |


   >[!IMPORTANT]
   >您只可以在建立 VM 時才可將 VM 置於可用性設定組。 在建立 VM 之後，即無法變更可用性設定組。 請參閱[管理虛擬機器的可用性](../manage-availability.md)。

Azure 會建立虛擬機器。

在虛擬機器建立後，設定網域控制站。

### <a name="configure-the-domain-controller"></a>設定網域控制站
在接下來的步驟中，請將 **ad-primary-dc** 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中開啟 [SQL-HA-RG] 資源群組，然後選取 [ad-primary-dc] 機器。 在 [ad-primary-dc] 刀鋒視窗中，按一下 [連接] 以開啟遠端桌面存取的 RDP 檔案。
   
    ![連接至虛擬機器](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. 以所設定的系統管理員帳戶 (**\DomainAdmin**) 和密碼 (**Contoso!0000**) 登入。
3. 根據預設，應會顯示 [伺服器管理員]  儀表板。
4. 按一下儀表板上的 [新增角色與功能]  連結。
   
    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。
6. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，加入這些角色所需的所有其他功能。
   
   > [!NOTE]
   > Windows 會顯示無靜態 IP 位址的警告。 如果您是要測試組態，請按一下 [繼續]。 針對生產環境情況，請在 Azure 入口網站中將 IP 位址設定為靜態，或 [使用 PowerShell 來設定網域控制站機器的靜態 IP 位址](../../../virtual-network/virtual-networks-reserved-private-ip.md)。
   > 
   > 
   
    ![新增角色對話方塊](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. 連續按 [下一步] 直到到達 [確認] 區段。 選取 [必要時自動重新啟動目的地伺服器]  核取方塊。
8. 按一下 [Install] 。
9. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
10. 在左側窗格中選取新的 [AD DS]  選項。
11. 按一下黃色警告列上的 [更多]  連結。
    
    ![在 DNS 伺服器 VM 上新增 AD DS 對話方塊](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. 在 [所有伺服器工作詳細資料] 對話方塊的 [動作] 欄中，按一下 [將此伺服器升級為網域控制站]。
13. 在 **Active Directory 網域服務組態精靈**中，使用下列值：
    
    | **Page** | 設定 |
    | --- | --- |
    | **部署組態** |**新增樹系**<br/> **根網域名稱** = corp.contoso.com |
    | **網域控制站選項** |**DSRM 密碼** = Contoso!0000<br/>**確認密碼** = Contoso!0000 |
14. 按 [下一步]，以通過精靈中的其他頁面。 在 [檢查先決條件] 頁面上，確認是否出現下列訊息：**已順利通過所有先決條件檢查**。 檢閱所有適用您情況的警告訊息，但是仍可以繼續進行安裝。
15. 按一下 [Install] 。 **ad-primary-dc** 虛擬機器會自動重新開機。

### <a name="note-ip-address-of-primary-domain-controller"></a>請注意主要網域控制站的 IP 位址

使用 DNS 的主要網域控制站。 請注意主要網域控制站 IP 位址。

取得主要網域控制站 IP 位址的方法之一是透過 Azure 入口網站。 

1. 在 Azure 入口網站中，開啟資源群組。 

1. 按一下主要網域控制站。

1. 在主要網域控制站刀鋒視窗中，按一下 [網路介面]。

![新增項目](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

請注意此伺服器的私人 IP 位址。 

### <a name="configure-the-second-domain-controller"></a>設定第二個網域控制站
在主要網域控制站重新開機之後，您可以設定第二個網域控制站。 這個選擇性步驟適用於高可用性。 請依照下列步驟設定第二個網域控制站：

1. 在入口網站中開啟 [SQL-HA-RG] 資源群組，然後選取 [ad-secondary-dc] 機器。 在 [ad-secondary-dc] 刀鋒視窗中，按一下 [連接] 以開啟遠端桌面存取的 RDP 檔案。
4. 使用所設定的系統管理員帳戶 (**BUILTIN\DomainAdmin**) 和密碼 (**Contoso!0000**) 登入 VM。
3. 將慣用 DNS 伺服器位址變更為網域控制站的位址。 
1. 在**網路和共用中心**，按一下 [網路介面]。 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. 按一下 [內容] 。
10. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)]  ，然後按一下 [內容]。
11. 選取 [使用下列的 DNS 伺服器位址]，並在 [慣用 DNS 伺服器] 中指定主要網域控制站的位址。
1. 依序按一下 [確定]、[關閉] 以認可變更。 現在您可以將 VM 加入 **corp.contoso.com**。

   >[!IMPORTANT]
   >如果您在變更 DNS 設定之後失去遠端桌面的連線，請移至 Azure 入口網站，然後重新啟動虛擬機器。

1. 從遠端桌面到次要網域控制站，開啟**伺服器管理員儀表板**。
4. 按一下儀表板上的 [新增角色與功能]  連結。
   
    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。
6. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，加入這些角色所需的所有其他功能。

9. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
10. 在左側窗格中選取新的 [AD DS]  選項。
11. 按一下黃色警告列上的 [更多]  連結。
12. 在 [所有伺服器工作詳細資料] 對話方塊的 [動作] 欄中，按一下 [將此伺服器升級為網域控制站]。
1. 在 [部署組態] 下，選取 [將網域控制站新增至現有網域]。 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)

1. 按一下 [選取...]。
1. 使用系統管理員帳戶 (**CORP.CONTOSO.COM\domainadmin**) 和密碼 (**Contoso!0000**) 進行連線。
1. 在 [從樹系中選取網域]，按一下您的網域，然後按一下 [確定]。 

1. 在 [網域控制站選項] 上，使用預設值並設定 DSRM 密碼。

   >[!NOTE]
   >**DNS 選項**頁面可能會警告您，無法建立此 DNS 伺服器的委派。 您在非生產環境中可以忽略這個警告。 
1. 按 [下一步]，直到對話方塊觸達 [必要條件] 檢查。 然後按一下 [安裝] 。

在伺服器完成組態變更後，重新啟動伺服器。 

### <a name=DomainAccounts></a> 設定網域帳戶

接下來的步驟，將帶您設定 Active Directory (AD) 帳戶。 下表顯示帳戶：

| |安裝帳戶<br/> |sqlserver-0 <br/>SQL Server 和 SQL Agent 服務帳戶 |sqlserver-1<br/>SQL Server 和 SQL Agent 服務帳戶
| --- | --- | --- | --- 
|**名字** |Install |SQLSvc1 | SQLSvc2
|**使用者 SamAccountName** |Install |SQLSvc1 | SQLSvc2

使用下列步驟建立每個帳戶。 

1. 重新登入 **ad-primary-dc** 電腦。
2. 在 [伺服器管理員] 中，選取 [工具]，然後按一下 [Active Directory 管理中心]。   
3. 從左窗格中選取 [corp (本機)]。
4. 在右側的 [工作] 窗格中，選取 [新增]，然後按一下 [使用者]。 
   ![Active Directory 管理中心](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >設定每個帳戶的複雜密碼。<br/> 對於非生產環境，將使用者帳戶設定為永不過期。

5. 按一下 [確定] 以建立使用者。 
6. 針對三個帳戶重複上述步驟。 

### <a name="grant-required-permissions-to-the-installation-account"></a>授與安裝帳戶必要權限
1. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。 然後在右側的 [工作] 窗格中，按一下 [內容]。
   
    ![企業使用者內容](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
8. 選取 [延伸模組]，然後按一下 [安全性] 索引標籤上的 [進階] 按鈕 。
9. 在 [企業的進階安全性設定]  對話方塊上。 按一下 [新增] 。
10. 再按一下 [選取主體]。 然後搜尋 **CORP\Install**。 按一下 [確定] 。
11. 勾選 [讀取全部內容]。

1. 勾選 [建立電腦物件]。
    
     ![企業使用者權限](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
12. 按一下 [確定]，再按一下 [確定]。 關閉 [企業內容] 視窗。

現在 Active Directory 和使用者物件已設定完畢，請建立兩個 SQL Server VM 及一個見證伺服器 VM。 然後，將三個 VM 都加入網域。

## <a name="create-sql-servers"></a>建立 SQL Server
### <a name="create-and-configure-the-sql-server-vms"></a>建立及設定 SQL Server VM
接下來，建立三個 VM，包括兩個 SQL Server VM 和一個適用於其他叢集節點的 VM。 若要建立每個 VM，請回到 **SQL-HA-RG** 資源群組，按一下 [新增]，依序搜尋適當的資源庫項目、按一下 [虛擬機器]，然後按一下 [從資源庫]。 使用下表中的資訊可協助您建立 VM：

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 選取適當的資源庫項目 |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| 虛擬機器組態 **基本** |**名稱** = cluster-fsw<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |**名稱** = sqlserver-0<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |**名稱** = sqlserver-1<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |
| 虛擬機器組態 **大小** |DS1\_V2 (單核心，3.5 GB) |**大小** = DS2\_V2 (2 核心，7 GB) |**大小** = DS2\_V2 (2 核心，7 GB) |
| 虛擬機器組態 **設定** |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |
| 虛擬機器組態 **SQL Server 設定** |不適用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |

<br/>

> [!NOTE]
> 此處建議的機器大小是為了在 Azure VM 中測試可用性群組。 為獲得生產工作負載的最佳效能，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中關於 SQL Server 機器大小和組態的建議。
> 
> 

完整佈建三個 VM 後，您必須將它們加入 **corp.contoso.com** 網域，並將 CORP\Install 管理權限授與給這些虛擬機器。

### <a name="set-dns-on-each-server"></a>在每一部伺服器上設定 DNS
首先，變更每部成員伺服器的慣用 DNS 伺服器位址。 請遵循下列步驟：

1. 在入口網站中，開啟 [SQL-HA-RG] 資源群組，然後選取 [sqlserver-0] 機器。 在 [sqlserver-0] 刀鋒視窗中，按一下 [連接] 以開啟遠端桌面存取的 RDP 檔案。
2. 以所設定的系統管理員帳戶 (**\DomainAdmin**) 和密碼 (**Contoso!0000**) 登入。
3. 根據預設，應會顯示 [伺服器管理員]  儀表板。 按一下左窗格中的 [本機伺服器]  。
5. 選取 [DHCP 指派的 IPv4 位址，支援 IPv6]  連結。
6. 在 [網路連接]  視窗中，選取網路圖示。
7. 在命令列中，按一下 [變更此連線的設定]。 如果看不到這個選項，按一下雙向右箭頭。
8. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)]  ，然後按一下 [內容]。
9. 選取 [使用下列的 DNS 伺服器位址]，並在 [慣用 DNS 伺服器] 中指定主要網域控制站的位址。
   >[!TIP]
   >若要取得伺服器的 IP 位址，使用 `nslookup`。<br/>
   >從命令提示字元中，輸入 `nslookup ad-primary-dc`。 
11. 依序按一下 [確定]、[關閉] 以認可變更。 

   >[!IMPORTANT]
   >如果您在變更 DNS 設定之後失去遠端桌面的連線，請移至 Azure 入口網站，然後重新啟動虛擬機器。

對所有伺服器重複這些步驟。

### <a name="joinDomain"></a>將伺服器加入網域。

現在您可以將 VM 加入 **corp.contoso.com**。 針對 SQL Server 和檔案共用見證伺服器執行下列動作︰ 

1. 遠端連線虛擬機器與 **BUILTIN\DomainAdmin**。 
1. 在 [伺服器管理員] 中，按一下 [本機伺服器]
1. 按一下 [工作群組] 連結。
1. 在 [電腦名稱] 區段中，按一下 [變更]。
1. 選取 [網域] 核取方塊，然後在文字方塊中輸入 **corp.contoso.com**。 按一下 [確定] 。
1. 在 [Windows 安全性] 快顯對話方塊中，指定預設的網域系統管理員帳戶 (**CORP\DomainAdmin**) 和密碼 (**Contoso!0000**) 的認證。
1. 出現「歡迎使用 corp.contoso.com 網域」的訊息時，請按一下 [確定] 。
1. 按一下 [關閉]，然後按一下快顯對話方塊中的 [立即重新啟動]。



### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>將 Corp\Install 使用者新增為每個叢集 VM 的系統管理員：

每個虛擬機器以網域的成員重新啟動之後，新增 **CORP\Install** 作為本機系統管理員群組的成員。 

1. 等候 VM 重新啟動，然後從主要網域控制站重新啟動 RDP 檔案，以使用 **CORP\DomainAdmin** 帳戶登入 **sqlserver-0**。
   >[!TIP]
   >請確定您使用網域系統管理員帳戶登入。 在先前步驟中，您使用內建系統管理員帳戶。 現在伺服器在網域中，使用網域帳戶。 在您的 RDP 工作階段中，指定 *DOMAIN*\\*username*。

2. 在 [伺服器管理員] 中選取 [工具]，然後按一下 [電腦管理]。
3. 在 [電腦管理 ] 視窗中，展開 [本機使用者和群組]，然後選取 [群組]。
4. 按兩下 [系統管理員]  群組。
5. 在 [系統管理員內容] 對話方塊中，按一下 [新增] 按鈕。
6. 輸入 **CORP\Install** 使用者，然後按一下 [確定]。 
7. 按一下 [確定]，以關閉 [系統管理員內容] 對話方塊。
8. 在 **sqlserver-1** 和 **cluster-fsw** 上重複上述步驟。

### <a name="setServiceAccount"></a>設定 SQL Server 服務帳戶

在每部 SQL Server 上，設定 SQL Server 服務帳戶。 使用您[設定網域帳戶](#DomainAccounts)時所建立的帳戶。

1. 啟動 **SQL Server 組態管理員**。

1. 以滑鼠右鍵按一下 SQL Server 服務SQL Server 服務，然後按一下 [屬性]。

1. 設定帳戶和密碼。 

1. 在其他 SQL Server 上重複這些步驟。  

針對 SQL Server 可用性群組，每個 SQL Server 必須以網域帳戶執行。 

### <a name="create-login-on-each-sql-server-for-installation-account"></a>在每個 SQL Server 上建立安裝帳戶登入

使用安裝帳戶來設定可用性群組。 這個帳戶在每個 SQL Server 上必須為**系統管理員**固定伺服器角色的成員。 下列步驟會建立安裝帳戶的登入︰

1. 使用 *\<MachineName\>\DomainAdmin* 帳戶 RDP 進入伺服器。

1. 開啟 SQL Server Management Studio，然後連線到 SQL Server 的本機執行個體。 

1. 在 [物件總管]**中**，按一下 [安全性]。

1. 以滑鼠右鍵按一下 [登入]。 按一下 [新增登入...]。

1. 在 [登入 - 新增] 中，按一下 [搜尋...]。

1. 按一下 [位置...]。

1. 輸入網域系統管理員網路認證。 

1. 使用安裝帳戶。

1. 將登入設定為**系統管理員**固定的伺服器角色的成員。 

1. 按一下 [確定]。 

在其他 SQL Server 上重複先前的步驟。 

## <a name="add-failover-cluster-features-to-both-sql-servers"></a>將容錯移轉叢集功能新增至兩個 SQL Server

若要新增容錯移轉叢集功能，在兩個 SQL Server 上執行下列步驟︰

1. 從遠端桌面到次要網域控制站，開啟**伺服器管理員儀表板**。
4. 按一下儀表板上的 [新增角色與功能]  連結。
   
    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. 連續選取 [下一步]，直到到達 [伺服器功能] 區段。
1. 在 [功能] 中，選取 [容錯移轉叢集]。 
1. 新增其他必要的功能。 
1. 按一下 [安裝] 以新增所有功能。

在其他 SQL Server 上重複這些步驟。 


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server"></a><a name="endpoint-firewall"> 在每個 SQL Server 上設定防火牆

方案需要在防火牆中開啟下列 TCP 通訊埠︰

- **SQL Server**<br/>
   SQL Server 預設執行個體的連接埠 1433。 
- **Azure Load Balancer 探查**<br/>
   任何可用的連接埠。 範例會經常使用 59999。
- **資料庫鏡像端點** <br/>
   任何可用的連接埠。 範例會經常使用 5022。 

必須在兩個 SQL Server 上開啟防火牆連接埠。

開啟連接埠的方式取決於您使用的防火牆解決方案。 下一節說明如何在 Windows 防火牆上開啟連接埠。 開啟每個 SQL Server 的必要通訊埠。 

### <a name="open-a-tcp-port-on-a-firewall"></a>開啟防火牆上的 TCP 連接埠 

1. 在第一個 SQL Server 上 [開始] 畫面中啟動 [具備進階安全性的 Windows 防火牆]。

2. 在左側窗格中，選取 [內送規則] 。 在右窗格中，按一下 [新增規則] 。

3. 針對 [規則類型]，選擇 [連接埠]。

1. 針對連接埠，指定 [TCP] 並輸入適當的連接埠號碼。 請參閱下列範例：

   ![SQLFirewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

1. 按 [下一步] 。 

5. 在 [動作] 頁面中，保持選取 [允許連接]，然後按 [下一步]。

6. 在 [設定檔] 頁面中，接受預設設定，然後按 [下一步]。

7. 在 [名稱] 頁面中，指定規則名稱，例如在 [名稱] 文字方塊中指定 [Azure LB Probe]，然後按一下 [完成]。

以相同的方式在第二個 SQL Server 上重複這些步驟。



## <a name="next-steps"></a>後續步驟

* [在 Azure 虛擬機器上建立 SQL Server Always On 可用性群組](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

