---
title: "在 Azure VM 中手動設定 Always On 可用性群組 - Microsoft Azure"
description: "使用 Azure 虛擬機器建立 Always On 可用性群組。 本教學課程主要是透過使用者介面作業，而非編寫指令碼。"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: timlt
editor: monicar
tags: azure-service-management
ms.assetid: 986cbc2e-553d-4eba-8acb-c34ad7fd1d8b
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/21/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 6b102884c7d5aafc85acdeb52a92b630e6e99b6a


---
# <a name="configure-always-on-availability-group-in-azure-vm-manually---resource-manager"></a>在 Azure VM 中手動設定 Always On 可用性群組 - Resource Manager
> [!div class="op_single_selector"]
> * [Resource Manager：範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Resource Manager︰手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [傳統：UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [傳統：PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

此端對端教學課程將示範如何在 Azure Resource Manager 虛擬機器上實作 SQL Server 可用性群組。 

在本教學課程中，您將建立下列元素：

* 包含兩個子網路 (前端和後端子網路) 的虛擬網路
* 可用性設定組中具有 Active Directory (AD) 網域的兩個網域控制站 
* 可用性設定組中已部署至後端子網路並加入 AD 網域的兩個 SQL Server VM
* 具有節點多數仲裁模型的 3 節點 WSFC 叢集
* 具有一或多個 IP 位址的內部負載平衡器，以支援一或多個可用性群組接聽程式
* 具有兩份可用性資料庫同步認可複本的可用性群組

下圖將解決方案以圖形呈現。

![Azure Resource Manager 中的 AG 架構](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

這是一個可能的組態。 您可以依需求進行修改。 例如，您可以使用網域控制站作為仲裁檔案共用見證，以減少虛擬機器數目。 這會減少含兩個複本的可用性群組的 VM 數目。 此方法可以從解決方案中減少一個 VM。

本教學課程會建立單一可用性群組，具有接聽程式的一個 IP 位址。 您也可以建立多個可用性群組，具有每個接聽程式的 IP 位址。 每個 IP 位址會使用相同的負載平衡器。 若要在負載平衡器上設定多個 IP 位址，請使用 PowerShell。 如需詳細資訊，請參閱[設定一或多個 Always On 可用性群組接聽程式 - Resource Manager](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

完成本教學課程需要幾個小時的時間，因為您必須建立並設定數個 Azure 虛擬機器。 您也可以自動建置這整個解決方案。 在 Azure 入口網站中，AlwaysOn 可用性群組有一個專用資源庫提供接聽程式。 資源庫安裝程式會自動設定可用性群組所需的一切項目。 如需詳細資訊，請參閱 [入口網站 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

本教學課程假設您已句備下列條件：

* 您已經有 Azure 帳戶。
* 您已經知道如何透過 GUI 佈建來自虛擬機器資源庫的 SQL Server VM。 如需詳細資訊，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 您已非常熟悉可用性群組的功能。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

> [!NOTE]
> 如果您對搭配 SharePoint 使用可用性群組感興趣，另請參閱 [設定 SharePoint 2013 的 SQL Server 2012 Always On 可用性群組](https://technet.microsoft.com/library/jj715261.aspx)。
> 
> 

## <a name="create-resource-group"></a>建立資源群組
1. 登入 [Azure 入口網站](http://portal.azure.com)。 
2. 按一下 [+新增]，然後在 [Marketplace] 搜尋視窗中輸入**資源群組**。
   
    ![資源群組](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)
3. 按一下 [資源群組]  
   
    ![新增資源群組](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)
4. 按一下 [建立] 。 
5. 在 [資源群組] 刀鋒視窗的 [資源群組名稱] 類型之下，輸入 **SQL-HA-RG**
6. 如果您有多個 Azure 訂用帳戶，請確認此訂用帳戶是您要在其中建立可用性群組的 Azure 訂用帳戶。 
7. 選取位置。 此位置是您要建立可用性群組的 Azure 區域。 在本教學課程中，我們將在一個 Azure 位置中建置所有資源。 
8. 確認已核取 [釘選到儀表板]  。 這個選擇性設定會在 Azure 入口網站儀表板上放置資源群組的捷徑。 
9. 按一下 [建立]  以建立資源群組。

Azure 會建立資源群組，並在入口網站中釘選資源群組的捷徑。

## <a name="create-network-and-subnets"></a>建立網路和子網路
下一個步驟是在 Azure 資源群組中建立網路和子網路。

此解決方案會使用具有兩個子網路的一個虛擬網路。 [Virtual Network Overview (虛擬網路概觀)](../virtual-network/virtual-networks-overview.md) 提供 Azure 中網路的詳細資訊。

若要建立虛擬網路：

1. 在 Azure 入口網站上，按一下新的資源群組，然後按一下 **+** 以將新的項目新增至資源群組。 Azure 即會開啟 [所有項目]  刀鋒視窗。 
   
   ![新增項目](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)
2. 搜尋 **虛擬網路**。
   
     ![搜尋虛擬網路](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)
3. 按一下 [虛擬網路] 。
4. 在 [虛擬網路] 刀鋒視窗中，按一下 [Resource Manager] 部署模型，然後按一下 [建立]。
   
     ![建立虛擬網路](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
5. 在 [建立虛擬網路]  刀鋒視窗上設定虛擬網路。 
   
   下表顯示虛擬網路的設定。
   
   | **欄位** | 值 |
   | --- | --- |
   | **名稱** |autoHAVNET |
   | **位址空間** |10.0.0.0/16 |
   | **子網路名稱** |Subnet-1 |
   | **子網路位址範圍** |10.0.0.0/24 |
   | **訂用帳戶** |指定您打算使用的訂用帳戶。 如果您只有一個訂用帳戶，則可讓此欄位留白。 |
   | **位置** |指定 Azure 位置。 |
   
    您的位址空間和子網路位址範圍可能與此表有所不同。 視您的訂用帳戶而定，Azure 會指定可用的位址空間和對應的子網路位址範圍。 如果沒有足夠的位址空間可供使用，請使用不同的訂用帳戶。 
6. 按一下 [建立] 
   
   ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

Azure 會讓您回到入口網站儀表板，並在建立完新網路時通知您。

### <a name="create-the-second-subnet"></a>建立第二個子網路
此時您的虛擬網路包含一個名為 Subnet-1 的子網路。 網域控制站會使用此子網路。 SQL Server 會使用名為 **Subnet-2**的第二個子網路。 設定 Subnet-2：

1. 在儀表板上，按一下您所建立的資源群組 [SQL-HA-RG] 。 在 [資源] 之下的資源群組中尋找此網路。
   
    如果看不到 [SQL-HA-RG]，請按一下 [資源群組] 並依資源群組名稱進行篩選，即可找到它。
2. 按一下資源清單上的 [autoHAVNET]  。 Azure 會開啟 [網路組態] 刀鋒視窗。
3. 在 **autoHAVNET** 虛擬網路上，按一下 [所有設定]。
4. 在 [設定] 刀鋒視窗上，按一下 [子網路]**。
   
    請注意您已建立的子網路。 
   
    ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)
5. 建立第二個子網路。 按一下 [+ 子網路] 。 
6. 在 [新增子網路] 刀鋒視窗中，於 [名稱] 之下輸入 **subnet-2** 來設定子網路。 Azure 會自動指定有效的 [位址範圍] 。 確認此位址範圍中至少有 10 個位址。 在生產環境中，您可能需要更多位址。 
7. 按一下 [確定] 。
   
    ![設定虛擬網路](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)

以下是虛擬網路和兩個子網路的組態設定摘要。

| **欄位** | 值 |
| --- | --- |
| **名稱** |**autoHAVNET** |
| **位址空間** |取決於您訂用帳戶中的可用位址空間。 一般的值是 10.0.0.0/16。 |
| **子網路名稱** |**Subnet-1** |
| **子網路位址範圍** |取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.0.0/24。 |
| **子網路名稱** |**Subnet-2** |
| **子網路位址範圍** |取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.1.0/24。 |
| **訂用帳戶** |指定您打算使用的訂用帳戶。 |
| **資源群組** |**SQL-HA-RG** |
| **位置** |指定您為資源群組選擇的相同位置。 |

## <a name="create-availability-sets"></a>建立可用性設定組
建立虛擬機器之前，您必須建立可用性設定組。 可用性設定組可縮短計劃性或非計劃性維護事件的停機時間。 Azure 可用性設定組是 Azure 置於實體容錯網域和更新網域上的邏輯資源群組。 容錯網域會確保可用性設定組的成員具有個別的電源和網路資源。 更新網域會確保可用性設定組的成員不會同時停機來進行維護。 [管理虛擬機器的可用性](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

您需要兩個可用性設定組。 一個用於網域控制站。 第二個用於 SQL Server。

若要建立可用性設定組，請移至資源群組，然後按一下 [新增]。 輸入**可用性設定組**以篩選結果。 按一下結果中的 [可用性設定組]。 按一下 [建立] 。

根據下表中的參數設定兩個可用性設定組。

| **欄位** | 網域控制站可用性設定組 | SQL Server 可用性設定組 |
| --- | --- | --- |
| **名稱** |adavailabilitySet |sqlAvailabilitySet |
| **資源群組** |SQL-HA-RG |SQL-HA-RG |
| **容錯網域** |3 |3 |
| **更新網域** |5 |3 |

建立可用性設定組之後，請回到 Azure 入口網站中的資源群組。

## <a name="create-domain-controllers"></a>建立網域控制站
在您建立網路、子網路、可用性設定組及網際網路對向負載平衡器之後， 您已準備好建立網域控制站的虛擬機器。

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>建立網域控制站的虛擬機器
若要建立和設定網域控制站，請回到 **SQL-HA-RG** 資源群組。

1. 按一下 [新增]。 [所有項目]  刀鋒視窗隨即開啟。
2. 輸入 **Windows Server 2012 R2 資料中心**。 
3. 按一下 [Windows Server 2012 R2 Datacenter] 。 在 [Windows Server 2012 R2 資料中心] 刀鋒視窗中，確認部署模型為 [Resource Manager]，然後按一下 [建立]。 Azure 會開啟 [建立虛擬機器]  刀鋒視窗。 

將上述步驟完成兩次以建立兩個虛擬機器。 為兩部虛擬機器命名︰

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** 是選擇性元件，可為 Active Directory 網域服務提供高可用性。 
  > 
  > 

下表顯示這兩部機器的設定。

| **欄位** | 值 |
| --- | --- |
| **使用者名稱** |DomainAdmin |
| **密碼** |Contoso!0000 |
| **訂用帳戶** |*您的訂用帳戶* |
| **資源群組** |SQL-HA-RG |
| **位置** |*您的位置* |
| **大小** |D1_V2 (標準) |
| **儲存體類型** |標準 |
| **儲存體帳戶** |*自動建立* |
| **虛擬網路** |autoHAVNET |
| **子網路** |Subnet-1 |
| **公用 IP 位址** |*與 VM 同名* |
| **網路安全性群組** |*與 VM 同名* |
| **診斷** |已啟用 |
| **診斷儲存體帳戶** |*自動建立* |
| **可用性設定組** |adavailabilitySet |

> [!NOTE]
> 在建立 VM 之後，即無法變更其相關的可用性設定組。
> 
> 

Azure 會建立虛擬機器。

在虛擬機器建立後，設定網域控制站。

### <a name="configure-the-domain-controller"></a>設定網域控制站
在接下來的步驟中，請將 **ad-primary-dc** 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中開啟 [SQL-HA-RG] 資源群組，然後選取 [ad-primary-dc] 機器。 在 [ad-primary-dc] 刀鋒視窗中，按一下 [連接] 以開啟遠端桌面存取的 RDP 檔案。
   
    ![連接至虛擬機器](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)
2. 以所設定的系統管理員帳戶 (**\DomainAdmin**) 和密碼 (**Contoso!0000**) 登入。
3. 根據預設，應會顯示 [伺服器管理員]  儀表板。
4. 按一下儀表板上的 [新增角色與功能]  連結。
   
    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)
5. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。
6. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，加入這些角色所需的所有其他功能。
   
   > [!NOTE]
   > Windows 會顯示無靜態 IP 位址的警告。 如果您是要測試組態，請按一下 [繼續]。 針對生產環境情況，請在 Azure 入口網站中將 IP 位址設定為靜態，或 [使用 PowerShell 來設定網域控制站機器的靜態 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。
   > 
   > 
   
    ![新增角色對話方塊](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)
7. 連續按 [下一步] 直到到達 [確認] 區段。 選取 [必要時自動重新啟動目的地伺服器]  核取方塊。
8. 按一下 [Install] 。
9. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
10. 在左側窗格中選取新的 [AD DS]  選項。
11. 按一下黃色警告列上的 [更多]  連結。
    
    ![在 DNS 伺服器 VM 上新增 AD DS 對話方塊](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)
12. 在 [所有伺服器工作詳細資料] 對話方塊的 [動作] 欄中，按一下 [將此伺服器升級為網域控制站]。
13. 在 **Active Directory 網域服務組態精靈**中，使用下列值：
    
    | **Page** | 設定 |
    | --- | --- |
    | **部署組態** |**新增樹系** = 已選取<br/>**根網域名稱** = corp.contoso.com |
    | **網域控制站選項** |**DSRM 密碼** = Contoso!0000<br/>**確認密碼** = Contoso!0000 |
14. 按 [下一步]，以通過精靈中的其他頁面。 在 [檢查先決條件] 頁面上，確認是否出現下列訊息：**已順利通過所有先決條件檢查**。 請注意，務必要檢閱所有適用您情況的警告訊息，但是仍可以繼續進行安裝。
15. 按一下 [Install] 。 **ad-primary-dc** 虛擬機器會自動重新開機。

### <a name="configure-the-second-domain-controller"></a>設定第二個網域控制站
在主要網域控制站重新開機之後，您可以設定第二個網域控制站。 這個選擇性步驟適用於高可用性。 若要完成此步驟，您必須知道網域控制站的私人 IP 位址。 您可以從 Azure 入口網站取得此資訊。 請依照下列步驟設定第二個網域控制站。

1. 登入 **ad-primary-dc** 電腦。 
2. 開啟遠端桌面並經由 IP 位址連接第二個網域控制站。 如果您不知道第二個網域控制站的 IP 位址，請移至 Azure 入口網站並檢查指派給第二個網域控制站之網路介面的位址。 
3. 將慣用 DNS 伺服器位址變更為網域控制站的位址。 
4. 啟動主要網域控制站 (**ad-primary-dc**) 的 RDP 檔案，並使用所設定的系統管理員帳戶 (**BUILTIN\DomainAdmin**) 和密碼 (**Contoso!0000**) 來登入 VM。
5. 從主要網域控制站，使用 IP 位址來啟動遠端桌面以連接到 **ad-secondary-dc** 。 使用相同的帳戶和密碼。
6. 登入後，會顯示 [伺服器管理員]  儀表板。 按一下左窗格中的 [本機伺服器]  。
7. 選取 [DHCP 指派的 IPv4 位址，支援 IPv6]  連結。
8. 在 [網路連接]  視窗中，選取網路圖示。
   
    ![變更 VM 慣用的 DNS 伺服器](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
9. 在命令列上按一下 [變更此連接的設定]  (視您的視窗大小而定，可能需按一下雙向右箭頭才能看到此命令)。
10. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)]  ，然後按一下 [內容]。
11. 選取 [使用下列的 DNS 伺服器位址]，並在 [慣用 DNS 伺服器] 中指定主要網域控制站的位址。
12. 此位址是指派給 Azure 虛擬網路 subnet-1 子網路中的 VM 的位址，而該 VM 為 **ad-primary-dc**。 若要確認 **ad-primary-dc** 的 IP 位址，請如下方所示，在命令提示字元中使用 **nslookup ad-primary-dc**。
    
    ![使用 NSLOOKUP 尋找 DC 的 IP 位址](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > 設定 DNS 之後，您可能會失去對成員伺服器的 RDP 工作階段。 如果您這樣做，請從 Azure 入口網站重新啟動 VM。
    > 
    > 
13. 依序按一下 [確定]、[關閉] 以認可變更。 現在您可以將 VM 加入 **corp.contoso.com**。
14. 重複您建立第一個網域控制站時所遵循的步驟，但是在「Active Directory 網域服務組態精靈」 中，請使用下列值：

| Page | 設定 |
| --- | --- |
| **部署組態** |**新增現有網域的網域控制站** = 已選取<br/>**根** = corp.contoso.com |
| **網域控制站選項** |**DSRM 密碼** = Contoso!0000<br/>**確認密碼** = Contoso!0000 |

### <a name="configure-domain-accounts"></a>設定網域帳戶
接下來的步驟，將帶您設定 Active Directory (AD) 帳戶，以供之後使用。

1. 重新登入 **ad-primary-dc** 電腦。
2. 在 [伺服器管理員] 中，選取 [工具]，然後按一下 [Active Directory 管理中心]。
   
    ![Active Directory 管理中心](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)
3. 在 [資源群組]  select  。
4. 在右側的 [工作] 窗格中，選取 [新增]，然後按一下 [使用者]。 套用下列設定：
   
   | 設定 | 值 |
   | --- | --- |
   |  **名字** |Install |
   |  **使用者 SamAccountName** |Install |
   |  **密碼** |Contoso!0000 |
   |  **確認密碼** |Contoso!0000 |
   |  **其他密碼選項** |已選取 |
   |  **密碼永久有效** |已檢查 |
5. 按一下 [確定]，以建立**安裝**使用者。 請使用此帳戶來設定容錯移轉叢集和可用性群組。
6. 透過相同的步驟建立兩個額外的使用者：**CORP\SQLSvc1** 和 **CORP\SQLSvc2**。 SQL Server 服務會使用這些帳戶。 接著，為 **CORP\Install** 提供必要的權限，以設定「Windows 服務容錯移轉叢集」(WSFC)。
7. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。 然後在右側的 [工作] 窗格中，按一下 [內容]。
   
    ![企業使用者內容](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)
8. 選取 [延伸模組]，然後按一下 [安全性] 索引標籤上的 [進階] 按鈕 。
9. 在 [企業的進階安全性設定]  對話方塊上。 按一下 [新增] 。
10. 再按一下 [選取主體]。 然後搜尋 **CORP\Install**。 按一下 [確定] 。
11. 選取 [讀取所有內容] 和 [建立電腦物件] 權限。
    
     ![企業使用者權限](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)
12. 按一下 [確定]，再按一下 [確定]。 關閉 [企業內容] 視窗。

現在 Active Directory 和使用者物件已設定完畢，請建立兩個 SQL Server VM 及一個見證伺服器 VM。 然後，將三個 VM 都加入網域。

## <a name="create-sql-servers"></a>建立 SQL Server
### <a name="create-and-configure-the-sql-server-vms"></a>建立及設定 SQL Server VM
接下來，建立三個 VM，包括兩個 SQL Server VM 和一個 WSFC 叢集節點。 若要建立每個 VM，請回到 **SQL-HA-RG** 資源群組，按一下 [新增]，依序搜尋適當的資源庫項目、按一下 [虛擬機器]，然後按一下 [從資源庫]。 然後使用下表中的範本建立 VM。

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 選取適當的資源庫項目 |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2** |**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2** |
| 虛擬機器組態 **基本** |**名稱** = cluster-fsw<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |**名稱** = sqlserver-0<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |**名稱** = sqlserver-1<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置** = 您的 Azure 位置 |
| 虛擬機器組態 **大小** |DS1 (1 核心、3.5 GB 記憶體) |**大小** = DS 2 (2 核心、7 GB 記憶體) |**大小** = DS 2 (2 核心、7 GB 記憶體) |
| 虛擬機器組態 **設定** |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = subnet-2(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = subnet-2(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體** = 進階 (SSD)<br/>**網路子網路** = autoHAVNET<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**子網路** = subnet-2(10.1.1.0/24)<br/>**公用 IP 位址** = 無<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |
| 虛擬機器組態 **SQL Server 設定** |不適用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |

<br/>

> [!NOTE]
> 先前的設定建議標準層虛擬機器，因為基本層機器不支援可用性群組接聽程式所需的負載平衡端點。 此外，此處建議的機器大小是為了在 Azure VM 中測試可用性群組。 為獲得生產工作負載的最佳效能，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中關於 SQL Server 機器大小和組態的建議。
> 
> 

完整佈建三個 VM 後，您必須將它們加入 **corp.contoso.com** 網域，並將 CORP\Install 管理權限授與給這些虛擬機器。

為了協助您繼續，請記下每個 VM 的 Azure 虛擬 IP 位址。 取得每部伺服器的 IP 位址。 在 Azure SQL-HA-RG 資源群組中，按一下 [autohaVNET]  資源。 [autohaVNET]  刀鋒視窗會顯示您網路中每個機器的 IP 位址。

記錄下列裝置的 IP 位址︰ 

| VM 角色 | 裝置 | IP 位址 |
| --- | --- | --- |
| 主要網域控制站 |ad-primary-dc | |
| 次要網域控制站 |ad-secondary-dc | |
| 叢集檔案共用見證 |cluster-fsw | |
| SQL Server |sqlserver-0 | |
| SQL Server |sqlserver-1 | |

請使用這些位址來設定每個 VM 的 DNS 服務。 針對這三個 VM 的每一個都執行下列步驟。

1. 首先，變更每部成員伺服器的慣用 DNS 伺服器位址。 
2. 啟動主要網域控制站 (**ad-primary-dc**) 的 RDP 檔案，並使用所設定的系統管理員帳戶 (**BUILTIN\DomainAdmin**) 和密碼 (**Contoso!0000**) 來登入 VM。
3. 從主要網域控制站，使用 IP 位址來啟動遠端桌面以連接到 **sqlserver-0** 。 使用相同的帳戶和密碼。
4. 登入後，會顯示 [伺服器管理員]  儀表板。 按一下左窗格中的 [本機伺服器]  。
5. 選取 [DHCP 指派的 IPv4 位址，支援 IPv6]  連結。
6. 在 [網路連接]  視窗中，選取網路圖示。
   
    ![變更 VM 慣用的 DNS 伺服器](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
7. 在命令列上按一下 [變更此連接的設定]  (視您的視窗大小而定，可能需按一下雙向右箭頭才能看到此命令)。
8. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)]  ，然後按一下 [內容]。
9. 選取 [使用下列的 DNS 伺服器位址]，並在 [慣用 DNS 伺服器] 中指定主要網域控制站的位址。
10. 此位址是指派給 Azure 虛擬網路 subnet-1 子網路中的 VM 的位址，而該 VM 為 **ad-primary-dc**。 若要確認 **ad-primary-dc** 的 IP 位址，請如下方所示，在命令提示字元中使用 **nslookup ad-primary-dc**。
    
    ![使用 NSLOOKUP 尋找 DC 的 IP 位址](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > 設定 DNS 之後，您可能會失去對成員伺服器的 RDP 工作階段。 如果您這樣做，請從 Azure 入口網站重新啟動 VM。
    > 
    > 
11. 依序按一下 [確定]、[關閉] 以認可變更。 現在您可以將 VM 加入 **corp.contoso.com**。
12. 回到 [本機伺服器] 視窗中，按一下 [工作群組] 連結。
13. 在 [電腦名稱] 區段中，按一下 [變更]。
14. 選取 [網域] 核取方塊，然後在文字方塊中輸入 **corp.contoso.com**。 按一下 [確定] 。
15. 在 [Windows 安全性] 快顯對話方塊中，指定預設的網域系統管理員帳戶 (**CORP\DomainAdmin**) 和密碼 (**Contoso!0000**) 的認證。
16. 出現「歡迎使用 corp.contoso.com 網域」的訊息時，請按一下 [確定] 。
17. 按一下 [關閉]，然後按一下快顯對話方塊中的 [立即重新啟動]。
18. 對檔案共用見證伺服器和每部 SQL Server 重複執行這些步驟。 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>將 Corp\Install 使用者新增為每個叢集 VM 的系統管理員：
1. 等候 VM 重新啟動，然後從主要網域控制站重新啟動 RDP 檔案，以使用 **BUILTIN\DomainAdmin** 帳戶登入 **sqlserver-0**。
2. 在 [伺服器管理員] 中選取 [工具]，然後按一下 [電腦管理]。
   
    ![電腦管理](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)
3. 在 [電腦管理 ] 視窗中，展開 [本機使用者和群組]，然後選取 [群組]。
4. 按兩下 [系統管理員]  群組。
5. 在 [系統管理員內容] 對話方塊中，按一下 [新增] 按鈕。
6. 輸入 **CORP\Install** 使用者，然後按一下 [確定]。 當系統提示您輸入認證時，請使用 **DomainAdmin** 帳戶搭配 **Contoso!0000** 密碼。
7. 按一下 [確定]，以關閉 [系統管理員內容] 對話方塊。
8. 在 **sqlserver-1** 和 **cluster-fsw** 上重複上述步驟。

## <a name="create-the-cluster"></a>建立叢集
### <a name="add-the-failover-clustering-feature-to-each-cluster-vm"></a>將 [容錯移轉叢集]  功能新增至每個叢集 VM。
1. 透過 RDP 連接到 **sqlserver-0**。
2. 在 [伺服器管理員] 儀表板中按一下 [新增角色及功能]。
3. 在**新增角色及功能精靈**中，連續按 [下一步] 直到到達 [功能] 頁面。
4. 選取 [容錯移轉叢集] 。 出現提示時，請新增所有其他相依功能。
   
    ![將 [容錯移轉叢集] 功能新增至 VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)
5. 按 [下一步]，然後按一下 [確認] 頁面上的 [安裝]。
6. **容錯移轉叢集**功能安裝完畢後，按一下 [關閉]。
7. 登出 VM。
8. 在 **sqlserver 1** 和 **cluster-fsw** 上重複本節中的步驟。

現在 SQL Server VM 已完成佈建並執行中，但這些 VM 所安裝的是使用預設值的 SQL Server。

### <a name="create-the-wsfc-cluster"></a>建立 WSFC 叢集
在本節中，您將建立 WSFC 叢集來裝載可用性群組。 到目前為止，您應該已針對 WSFC 叢集中三個 VM 中的每一個都完成下列作業：

* 在 Azure 中完整佈建
* 將 VM 加入網域
* 將 **CORP\Install** 新增至本機系統管理員群組
* 新增 [容錯移轉叢集] 功能

以上都是將 VM 加入 WSFC 叢集前，每個 VM 所需完成的先決條件。

此外，請注意 Azure 虛擬網路的運作方式和其在內部部署網路中的運作方式不同。 您必須依照下列順序建立叢集：

1. 在其中一個節點 (**sqlserver-0**) 上建立單一節點叢集。
2. 將叢集 IP 位址修改成 **sqlsubnet**中未使用的 IP 位址。
3. 讓叢集名稱上線。
4. 新增其他節點 (**sqlserver 1** 和 **cluster-fsw**)。

依照下列步驟來設定叢集。

1. 啟動 **sqlserver-0** 的 RDP 檔案，並使用網域帳戶 **CORP\Install** 登入。
2. 在 [伺服器管理員] 儀表板中，選取 [工具]，然後按一下 [容錯移轉叢集管理員]。
3. 如下方所示，在左窗格中，以滑鼠右鍵按一下 [容錯移轉叢集管理員]，再按一下 [建立叢集]。
   
    ![建立叢集](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)
4. 在建立叢集精靈中，透過套用下方設定以通過精靈中的頁面，來建立單節點叢集：
   
   | Page | 設定 |
   | --- | --- |
   | 開始之前 |使用預設值 |
   | 選取伺服器 |在 [輸入伺服器名稱] 中輸入 **sqlserver-0**，然後按一下 [新增] |
   | 驗證警告 |選取 [**否。我不需要 Microsoft 提供此叢集的支援，也不需要執行驗證測試。請在我按 [下一步] 後，繼續建立叢集**。 |
   | 用於管理叢集的存取點 |在 [叢集名稱] 中輸入 **Cluster1** |
   | 確認 |除非您使用的是儲存空間，否則請使用預設值。 請詳閱此表之後的備註。 |

    >[AZURE.NOTE] 如果您使用的是會將多個磁碟組成存放集區的[儲存空間](https://technet.microsoft.com/library/hh831739)，您就必須在 [確認] 頁面上取消勾選 [新增適合的儲存裝置到叢集] 核取方塊。 如果不將此選項取消勾選，Windows 就會在進行叢集程序時將虛擬磁碟中斷連結。 因此，它們將不會顯示在「磁碟管理員」或「總管」中，直到您使用 PowerShell 將儲存空間從叢集中移除後再重新連接為止。

    您現已建立叢集，請確認組態並加入其餘節點。 

1. 在中央窗格中向下捲動至 [叢集核心資源] 區段，並展開 [名稱：Clutser1] 的詳細資料。 在 [失敗] 狀態中，應該會同時出現 [名稱] 和 [IP 位址] 資源 。 由於指派給叢集的 IP 位址與虛擬機器的 IP 位址相同，是個重複的位址，因此無法讓該 IP 位址資源上線。
2. 以滑鼠右鍵按一下失敗的 [IP 位址] 資源，然後按一下 [內容]。
   
    ![叢集屬性](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)
3. 選取 [靜態 IP 位址]，並在 [位址] 文字方塊中指定 subnet-2 中可用的位址。 然後按 [下一步] 。
4. 在 [叢集核心資源] 區段中，以滑鼠右鍵按一下 [名稱：Cluster1]，再按一下 [上線]。 然後等待兩個資源上線。 叢集名稱資源上線後，會以新的 AD 電腦帳戶更新 DC 伺服器。 稍後請使用此 AD 帳戶來執行可用性群組叢集服務。
5. 最後，將剩餘的節點新增至叢集。 如下所示，在瀏覽器樹狀目錄中以滑鼠右鍵按一下 [Cluster1.corp.contoso.com]，再按一下 [新增節點]。
   
    ![將節點新增至叢集](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)
6. 在 [新增節點精靈] 中，按 [下一步]。 在 [選取伺服器] 頁面上，於 [輸入伺服器名稱] 中輸入伺服器名稱，然後按一下 [新增]，將 **sqlserver-1** 和 **cluster-fsw** 新增至清單。 完成之後，按 [下一步]。
7. 在 [驗證警告] 頁面上，按一下 [否] (實際操作時，請執行驗證測試)。 然後按 [下一步] 。
8. 在 [確認] 頁面中按 [下一步]，以新增節點。
   
   > [!WARNING]
   > 如果您使用的是會將多個磁碟組成存放集區的[儲存空間](https://technet.microsoft.com/library/hh831739)，您就必須取消勾選 [新增適合的儲存裝置到叢集] 核取方塊。 如果不取消勾選此選項，在群集程序進行期間虛擬磁碟會中斷連結。 因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。
   > 
   > 
9. 將節點新增至叢集後，請按一下 [完成]。 容錯移轉叢集管理員現在應該會顯示您的叢集具有三個節點，並將這些節點列在**節點**容器中。
10. 登出遠端桌面工作階段。

## <a name="configure-availability-groups"></a>設定可用性群組
本節將針對 **sqlserver-0** 和 **sqlserver-1** 進行下列工作：

* 將 **CORP\Install** 新增為預設 SQL Server 執行個體的系統管理員角色
* 開啟防火牆，以便從遠端存取 SQL Server、資料庫監視端點及 Azure Load Balancer 探查連接埠 
* 啟用可用性群組功能
* 將 SQL Server 服務帳戶分別變更為 **CORP\SQLSvc1** 和 **CORP\SQLSvc2**

執行這些動作沒有順序限制。 不過，下列步驟將依順序執行這些動作。 依照 **sqlserver-0** 和 **sqlserver-1** 的步驟執行：

### <a name="add-installation-account-as-sysadmin-fixed-server-role-on-each-sql-server"></a>將安裝帳戶新增為每部 SQL Server 上的 sysadmin 固定伺服器角色
1. 如果您尚未將 VM 登出遠端桌面工作階段，請現在登出。
2. 啟動 **sqlserver-0** 和 **sqlserver-1** 的 RDP 檔案，並以 **BUILTIN\DomainAdmin** 身分登入。
3. 啟動 **SQL Server Management Studio**，將 **CORP\Install** 新增為預設 SQL Server 執行個體的 **sysadmin** 角色。 在 [物件總管] 中，以滑鼠右鍵按一下 [登入]，再按一下 [新增登入]。
4. 在 [登入名稱] 中輸入 **CORP\Install**。
5. 在 [伺服器角色] 頁面上，選取 [系統管理員]。 然後按 [下一步] 。 建立登入之後，展開 [物件總管] 中的 [登入] 便可看到該登入。

### <a name="open-the-firewall-for-remote-access-to-sql-server-and-the-probe-port-on-each-sql-server"></a>開啟防火牆，以便遠端存取 SQL Server 和每部 SQL Server 上的探查連接埠
此解決方案要求每部 SQL Server 有兩個防火牆規則。 第一個規則提供 SQL Server 的輸入存取，第二個規則提供負載平衡器和接聽程式的輸入存取。 

1. 接下來，建立 SQL Server 的防火牆規則。 在 [開始] 畫面中啟動 [具備進階安全性的 Windows 防火牆]。
2. 在左側窗格中，選取 [內送規則] 。 在右窗格中，按一下 [新增規則] 。
3. 在 [規則類型] 頁面中，選取 [程式]，然後按 [下一步]。
4. 在 [程式] 頁面中，選取 [此程式路徑]，並在文字方塊中輸入 **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** (如果您使用的是 SQL Server 2012、則 SQL Server 的目錄為 **MSSQL11.MSSQLSERVER**)。 然後按 [下一步] 。
5. 在 [動作] 頁面中，保持選取 [允許連接]，然後按 [下一步]。
6. 在 [設定檔] 頁面中，接受預設設定，然後按 [下一步]。
7. 在 [名稱] 頁面中，指定規則名稱，例如在 [名稱] 文字方塊中指定 [SQL Server (程式規則)]，然後按一下 [完成]。
8. 為探查連接埠建立額外的輸入防火牆規則。 基於本教學課程的目的，此規則是 TCP 連接埠 59999 的輸入規則。 將規則命名為 **SQL Server 接聽程式**。

在兩部 SQL Server 上完成所有步驟。

### <a name="enable-availability-groups-feature-on-each-sql-server"></a>在每部 SQL Server 上啟用可用性群組功能
在兩部 SQL Server 上執行這些步驟。 

1. 接下來，啟用 [AlwaysOn 可用性群組]  功能。 在 [開始] 畫面中，啟動 [SQL Server 組態管理員]。
2. 在瀏覽器樹狀目錄中按一下 [SQL Server 服務]，然後以滑鼠右鍵按一下 [SQL Server (MSSQLSERVER)] 服務，再按一下 [內容]。
3. 如下方所示，按一下 [AlwaysOn 高可用性] 索引標籤，然後選取 [啟用 AlwaysOn 可用性群組]，再按一下 [套用]。 按一下快顯對話方塊中的 [確定]  ，先不要關閉 [內容] 視窗。 變更服務帳戶之後，將重新啟動 SQL Server 服務。
   
    ![啟用 AlwaysOn 可用性群組](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### <a name="set-the-sql-server-service-account-on-each-sql-server"></a>在每部 SQL Server 上設定 SQL Server 服務帳戶
在兩部 SQL Server 上執行這些步驟。

1. 接下來，變更 SQL Server 服務帳戶。 按一下 [登入] 索引標籤，在 [帳戶名稱] 中輸入 **CORP\SQLSvc1** (若為 **sqlserver-0**) 或 **CORP\SQLSvc2** (若為 **sqlserver-1**)，填入密碼並加以確認，然後按一下 [確定]。
2. 在快顯視窗中按一下 [是]  ，重新啟動 SQL Server 服務。 SQL Server 服務重新啟動之後，您在 [內容] 視窗中所進行的變更便會生效。
3. 登出所有 VM。

### <a name="create-the-availability-group"></a>建立可用性群組。
現在已準備就緒，可以開始設定可用性群組。 以下列出需執行的動作：

* 在 **sqlserver 0** 上建立新資料庫 (**MyDB1**)
* 建立資料庫的完整備份和交易記錄備份
* 透過 [NORECOVERY] 選項將完整備份和記錄備份還原至 **sqlserver-1**
* 透過同步認可、自動容錯移轉，和可讀取的次要複本建立可用性群組 (**AG1**)

### <a name="create-the-mydb1-database-on-sqlserver-0"></a>在 sqlserver-0 上建立 MyDB1 資料庫：
1. 如果您有您尚未將 **sqlserver-0** 和 **sqlserver-1** 登出遠端桌面工作階段，請立即登出。
2. 啟動 **sqlserver-0** 的 RDP 檔案，並以 **CORP\Install** 的身分登入。
3. 在 [檔案總管] 中，於 **C:\** 磁碟中，建立名為**備份**的目錄。 此目錄將用來備份和還原您的資料庫。
4. 如下方所示，以滑鼠右鍵按一下新目錄，指向 [共用對象]，然後按一下 [特定人員]。
   
    ![建立備份資料夾](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)
5. 如下方所示，新增 **CORP\SQLSvc1** 並授與 **讀取/寫入**權限，再新增 **CORP\SQLSvc2** 並授與**讀取/寫入**權限，然後按一下 [共用]。 檔案共用程序完成後，按一下 [完成] 。
   
    ![將權限授與備份資料夾](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)
6. 接下來，建立資料庫。 透過 [開始] 功能表，啟動 **SQL Server Management Studio**，然後按一下 [連接]，連接至預設的 SQL Server 執行個體。
7. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫]，再按一下 [新增資料庫]。
8. 在 [資料庫名稱] 中，輸入 **MyDB1**，然後按一下 [確定]。

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-sqlserver-1"></a>建立 MyDB1 的完整備份，然後將其還原至 sqlserver-1：
1. 接下來，備份資料庫的所有內容。 在 [物件總管] 中，展開 [資料庫]，以滑鼠右鍵按一下 [MyDB1]，指向 [工作]，然後按一下 [備份]。
2. 在 [來源] 區段中，讓 [備份類型] 的設定保持為 [完整]。 在 [目的地] 區段中按一下 [移除]，移除備份檔案的預設檔案路徑。
3. 在 [目的地] 區段中，按一下 [新增]。
4. 在 [檔案名稱] 文字方塊中，輸入 **\\\\sqlserver-0\backup\MyDB1.bak**。 接著，按一下 [確定]，再按一下 [確定]，以備份資料庫。 備份作業完成後，再次按一下 [確定]  ，關閉對話方塊。
5. 接下來，備份資料庫的交易記錄。 在 [物件總管] 中，展開 [資料庫]，以滑鼠右鍵按一下 [MyDB1]，指向 [工作]，然後按一下 [備份]。
6. 在 [備份類型] 中，選取 [交易記錄]。 讓**目的地**的檔案路徑設定保持為您稍早指定的路徑，並按一下 [確定]。 備份作業完成後，再次按一下 [確定]。
7. 接下來，將完整備份和交易記錄備份還原至 **sqlserver-1**。 啟動 **sqlserver-1** 的 RDP 檔案，並以 **CORP\Install** 的身分登入。 讓 **sqlserver-0** 的遠端桌面工作階段保持開啟狀態。
8. 透過 [開始] 功能表，啟動 **SQL Server Management Studio**，然後按一下 [連接]，連接至預設的 SQL Server 執行個體。
9. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫]，再按一下 [還原資料庫]。
10. 在 [來源] 區段中，選取 [裝置]，然後按一下 […] 按鈕  按鈕。
11. 在 [選取備份裝置] 中按一下 [新增]。
12. 在 [備份檔案位置] 中，輸入 **\\\\sqlserver-0\backup**，按一下 [重新整理]，選取 [MyDB1.bak]，按一下 [確定]，然後再按一次 [確定]。 現在 [還原] 窗格的備份集，應會顯示可以在完整備份和記錄備份。
13. 移至 [選項] 頁面中，在還原狀態中選取 [使用 NORECOVERY 還原]，然後按一下 [確定]，以還原資料庫。 還原作業完成後，按一下 [確定]。

### <a name="create-the-availability-group"></a>建立可用性群組：
1. 回到 **sqlserver-0**的遠端桌面工作階段。 如下所示，在 SSMS 的 [物件總管] 中，以滑鼠右鍵按一下 [AlwaysOn 高可用性]，再按一下 [新增可用性群組精靈]。
   
    ![啟動新增可用性群組精靈](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)
2. 在 [簡介] 頁面上，按 [下一步]。 在 [指定可用性群組名稱] 頁面，於 [可用性群組名稱] 中輸入 [AG1]，然後再次按 [下一步]。
   
    ![新增 AG 精靈：指定 AG 名稱](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)
3. 在 [選取資料庫] 頁面中，選取 [MyDB1]，然後按 [下一步]。 由於您已經為預定的主要複本建立了至少一份完整備份，所以現在這些資料庫已符合建立可用性群組的先決條件。
   
    ![新增 AG 精靈：選取資料庫](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)
4. 在 [指定複本] 頁面中，按一下 [新增複本]。
   
    ![新增 AG 精靈：指定複本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)
5. 隨即跳出 [連接至伺服器] 對話方塊。 在 [伺服器名稱] 中輸入 **sqlserver-1**，然後按一下 [連接]。
   
    ![新增 AG 精靈：連接至伺服器](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)
6. 回到 [指定複本] 頁面，現在 [可用性複本] 中應會列出 **sqlserver-1**。 如下方所示，設定複本。 
   
    ![新增 AG 精靈：指定複本 (完成)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)
7. 按一下 [端點]  以查看此可用性群組將使用的資料庫鏡像端點。 每個 SQL Server 執行個體都必須有一個資料庫鏡像端點。 請記下精靈為此端點指定的 TCP 連接埠。 在每一部伺服器上為此 TCP 連接埠建立輸入防火牆規則。
   
    完成後，請按 [下一步] 。
8. 在 [選取初始資料同步處理] 頁面中，按一下 [僅聯結]，然後按 [下一步]。 您在建立 **sqlserver-0** 的完整備份和交易備份，並將備份還原至 **sqlserver-1** 時，便已手動執行資料同步處理。 您可以改為選擇不在資料庫上執行備份與還原作業，並選取 [完整]  讓新可用性群組精靈為您執行資料同步處理。 不過，不建議針對某些企業中的超大型資料庫採用這種同步處理方式。
   
    ![新增 AG 精靈：選取初始資料同步處理](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)
9. 在 [驗證] 頁面中按 [下一步]。 此頁面的外觀類似於下圖。 由於您尚未設定可用性群組接聽程式，所以出現了關於接聽程式組態的警告。 您可以忽略此警告，因為本教學課程不會示範設定接聽程式的步驟。 本教學課程可讓您稍後建立接聽程式。
   
    ![新增 AG 精靈：驗證](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)
10. 在 [摘要] 頁面中，按一下 [完成]，然後等候精靈設定新的可用性群組。 在 [進度] 頁面中，按一下 [詳細資料] 可檢視詳細的進度。 精靈完成後，如下方所示，檢查 [結果] 頁面，確認是否已成功建立可用性群組，然後按一下 [關閉] 以結束精靈。
    
     ![新增 AG 精靈：結果](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)
11. 在 [物件總管] 中，展開 [AlwaysOn 高可用性]，再展開 [可用性群組]。 新的可用性群組應會顯示在此容器中。 以滑鼠右鍵按一下 [AG1 (主要)]，再按一下 [顯示儀表板]。
    
     ![顯示 AG 儀表板](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)
12. [AlwaysOn 儀表板]  的外觀應類似於下圖。 當中會顯示複本、每個複本的容錯移轉模式和同步處理狀態。
    
     ![AG 儀表板](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)
13. 回到 [伺服器管理員]，選取 [工具]，然後啟動 [容錯移轉叢集管理員]。
14. 展開 [Cluster1.corp.contoso.com]，然後展開 [服務和應用程式]。 選取 [角色]。請注意，先前已建立了 **AG1** 的可用性群組角色。 請注意，由於您未設定接聽程式，所以 AG1 不具備任何資料庫用戶端可用來連接至可用性群組的 IP 位址。 直接連接至主要節點，可進行讀寫作業，連接至次要節點，則可進行唯讀查詢。
    
    ![容錯移轉叢集管理員中的 AG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)
    
    > [!WARNING]
    > 請勿嘗試透過容錯移轉叢集管理員，容錯移轉可用性群組。 所有容錯移轉作業都應在 SSMS 的 **AlwaysOn 儀表板** 中執行。 如需詳細資訊，請參閱 [容錯移轉叢集和 AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/ff929171.aspx)。
    > 
    > 

## <a name="configure-internal-load-balancer"></a>設定內部負載平衡器
為了直接連線到可用性群組，您必須設定負載平衡器。 負載平衡器會將用戶端流量導向到繫結至接聽程式 IP 位址且在探查連接埠上的 VM。 本教學課程使用的是內部負載平衡器 (或稱 ILB)。 ILB 允許讓來自相同虛擬網路內的流量連線到 SQL Server。 應用程式如果需要透過網際網路連接到 SQL Server，就需要使用網際網路面向 (或外部) 負載平衡器。 如需詳細資訊，請參閱 [Azure Load Balancer 概觀](../load-balancer/load-balancer-overview.md)。

> [!NOTE]
> 本教學課程說明如何建立單一接聽程式 - 使用一個 ILB IP 位址。 若要使用一或多個 IP 位址建立一或多個接聽程式，請參閱[建立可用性群組接聽程式和負載平衡器 | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
> 
> 

### <a name="create-the-load-balancer-in-azure"></a>在 Azure 中建立負載平衡器
1. 在 Azure 入口網站中，移至 **SQL-HA-RG**，然後按一下 [+ 新增]。
2. 搜尋 [負載平衡器] 。 選擇 Microsoft 所發行的負載平衡器，然後按一下 [建立] 。
3. 設定負載平衡器的下列參數。

| 設定 | 欄位 |
| --- | --- |
| **名稱** |sqlLB |
| **配置** |內部 |
| **虛擬網路** |autoHAVNET |
| **子網路** |subnet-2 這是您將為叢集資源中的接聽程式設定的 IP 位址。 |
| **IP 位址指派** |靜態 |
| **IP 位址** |使用 subnet-2 中的可用位址。 |
| **訂用帳戶** |使用與此解決方案中所有其他資源相同的訂用帳戶。 |
| **位置** |使用與此解決方案中所有其他資源相同的位置。 |

按一下 [建立] 。

在負載平衡器上進行下列設定︰

| 設定 | 欄位 |
| --- | --- |
| **後端集區** 名稱 |sqlLBBE |
| **SQLLBBE 可用性設定組** |sqlAvailabilitySet |
| **SQLLBBE 虛擬機器** |sqlserver-0、sqlserver-1 |
| **SQLLBBE 使用者** |SQLAlwaysOnEndPointListener |
| **探查** 名稱 |SQLAlwaysOnEndPointProbe |
| **探查通訊協定** |TCP |
| **探查連接埠** |59999 - 請注意，您可以使用任何未使用的連接埠。 |
| **探查間隔** |5 |
| **探查狀況不良臨界值** |2 |
| **探查使用者** |SQLAlwaysOnEndPointListener |
| **負載平衡規則** 名稱 |SQLAlwaysOnEndPointListener |
| **負載平衡規則通訊協定** |TCP |
| **負載平衡規則連接埠** |1433 * |
| **負載平衡規則後端連接埠** |1433 |
| **負載平衡規則探查** |SQLAlwaysOnEndPointProbe |
| **負載平衡規則工作階段持續性** |None |
| **負載平衡規則閒置逾時** |4 |
| **負載平衡規則浮動 IP (伺服器直接回傳)** |已啟用 |

> * 1433 是預設的 SQL Server 連接埠。 用來作為預設執行個體的前端連接埠。 如果您需要多個可用性群組，您將需要為每個可用性群組建立額外的 IP 位址。 每個可用性群組將需要有自己的前端連接埠。 請參閱 [建立可用性群組接聽程式和負載平衡器 |Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
> 
> [!NOTE]
> 您必須於建立時在負載平衡規則中啟用伺服器直接回傳。
> 
> 

設定負載平衡器之後，請在容錯移轉叢集上設定接聽程式。 

## <a name="configure-the-listener"></a>設定接聽程式
下一步是在容錯移轉叢集上設定可用性群組接聽程式。 

1. 透過 RDP 連接到 SQL Server (從 ad-primary-dc 到 sqlserver-0)。
2. 在 [容錯移轉叢集管理員] 中，記下叢集網路的名稱。 若要在 [容錯移轉叢集管理員] 中判斷叢集網路名稱，請按一下左窗格中的 [網路]。 您將在 PowerShell 指令碼的 `$ClusterNetworkName` 變數中使用這個名稱。
3. 在 [容錯移轉叢集管理員] 中，展開叢集名稱，然後按一下 [角色] 。
4. 在 [角色] 窗格中，於可用性群組名稱上按一下滑鼠右鍵，然後選取 [加入資源] > [用戶端存取點]。 
5. 對於 [名稱]，輸入 **aglistener**。 按兩次 [下一步]，然後按一下 [完成]。 目前請勿讓接聽程式或資源上線工作。
6. 按一下 [資源]  索引標籤，然後展開您剛才建立的用戶端存取點。 以滑鼠右鍵按一下 IP 資源，然後按一下 [屬性]。 請記下 IP 位址的名稱。 請在 PowerShell 指令碼的 `$IPResourceName` 變數中使用這個名稱。
7. 按一下 [IP 位址] 之下的 [靜態 IP 位址]，並將靜態 IP 位址設定為您在 Azure 入口網站上用於 **sqlLB** 負載平衡器的相同位址。 請在 PowerShell 指令碼的 `$ILBIP` 變數中使用這個相同的 IP 位址。 
8. 針對此位址停用 NetBIOS，然後按一下 [確定] 。 
9. 在目前裝載主要複本的叢集節點上，開啟已提升權限的 PowerShell ISE，並將下列命令貼到新的指令碼中。
   
    ```
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # In this sample we've using 59999 for the probe port. 
   
    Import-Module FailoverClusters
   
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
10. 更新變數並執行 PowerShell 指令碼，以設定新接聽程式的 IP 位址和連接埠。
11. 在 [容錯移轉叢集管理員] 中，以滑鼠右鍵按一下可用性群組資源，然後按一下 [屬性]。 在 [相依性]  索引標籤上，將資源群組設定為相依於接聽程式網路名稱。 
12. 將接聽程式連接埠屬性設定為 1433。 若要這樣做，請開啟 SQL Server Management Studio，在可用性群組接聽程式上按一下滑鼠右鍵，然後選取 [屬性]。 將 [連接埠]  設定為 1433。 您應該使用您為 SQL Server 設定的相同連接埠號碼。 
13. 此時，您可以使接聽程式上線。 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [線上工作] 。

### <a name="test-the-connection-to-the-listener"></a>測試接聽程式的連線
若要測試連線︰

1. 透過 RDP 連接到未擁有複本的 SQL Server。
2. 使用 sqlcmd 公用程式來測試連線。 例如，下列指令碼會透過接聽程式與 Windows 驗證來建立主要複本的 sqlcmd 連線︰
   
    ```
    sqlcmd -S "<listenerName>" -E
    ```
   
   如果接聽程式使用的是 1433 以外的連接埠，您就需要在測試中指定該連接埠號碼。 例如，下列查詢會使用連接埠 1435 來測試對接聽程式的連線：
   
    ```
    sqlcmd -S "<listenerName>",1435 -E
    ```

## <a name="next-steps"></a>後續步驟
如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




<!--HONumber=Nov16_HO3-->


