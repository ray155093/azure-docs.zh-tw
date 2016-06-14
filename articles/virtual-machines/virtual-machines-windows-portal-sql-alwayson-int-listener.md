<properties
   pageTitle="在 Azure 虛擬機器中建立 SQL Server 的 AlwaysOn 可用性群組接聽程式"
   description="在 Azure 虛擬機器中建立 SQL Server 的 AlwaysOn 可用性群組接聽程式的逐步指示"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="04/17/2016"
   ms.author="MikeRayMSFT"/>

# 在 Azure 中設定 AlwaysOn 可用性群組的內部負載平衡器

本主題說明如何在資源管理員模型中執行的 Azure 虛擬機器中建立 SQL Server AlwaysOn 可用性群組的內部負載平衡器。當 SQL Server 執行個體位於 Azure 虛擬機器時，AlwaysOn 可用性群組需要負載平衡器。負載平衡器會儲存可用性群組接聽程式的 IP 位址。如果可用性群組跨越多個區域，則每個區域都需要負載平衡器。

若要完成這項工作，您需要在資源管理員模型中的 Azure 虛擬機器上部署 SQL Server AlwaysOn 可用性群組。這兩部 SQL Server 虛擬機器必須屬於相同的可用性設定組。您可以使用 [Microsoft 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)在 Azure Resource Manager 中自動建立 AlwaysOn 可用性群組。此範本會自動為您建立內部負載平衡器。

如果您想要的話，可以[手動設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

這個主題需要已設定可用性群組。

相關主題包括：

 - [在 Azure VM (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [使用 Azure Resource Manager 和 PowerShell 來設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## 步驟

依照這份文件逐步執行，在 Azure 入口網站中建立和設定負載平衡器。完成之後，您將設定叢集，以將來自負載平衡器的 IP 位址使用於 AlwaysOn 可用性群組接聽程式。

## 在 Azure 入口網站中建立及設定負載平衡器

在這部分的工作中，您會在 Azure 入口網站中執行下列步驟︰

1. 建立負載平衡器和設定 IP 位址

1. 設定後端集區

1. 建立探查

1. 設定負載平衡規則

>[AZURE.NOTE] 如果 SQL Server 位於不同的資源群組和區域中，您就要進行上述所有步驟兩次，在每個資源群組中各一次。

## 1\.建立負載平衡器和設定 IP 位址

第一個步驟是建立負載平衡器。在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。在資源群組中，按一下 [新增]。

- 搜尋**負載平衡器**。從搜尋結果中選取由 **Microsoft** 發行的 [負載平衡器]。

- 在 [負載平衡器] 刀鋒視窗上，按一下 [建立]。

- 在 [建立負載平衡器] 上，依下列方式設定負載平衡器︰

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 代表負載平衡器的文字名稱。例如 **sqlLB**。 |
| **結構描述** | **內部** |
| **虛擬網路** | 選擇 SQL Server 所在的虛擬網路。 |
| **子網路** | 選擇 SQL Server 所在的子網路。 |
| **訂用帳戶** | 如果您有多個訂用帳戶，此欄位才會出現。選取您想要與此資源相關聯的訂用帳戶。通常是與可用性群組的所有資源相同的訂用帳戶。 |
| **資源群組** | 選擇 SQL Server 所在的資源群組。 | 
| **位置** | 選擇 SQL Server 所在的 Azure 位置。 |

- 按一下 [建立]。 

Azure 會建立您在上面設定的負載平衡器。此負載平衡器屬於特定的網路、子網路、資源群組和位置。Azure 完成之後，請確認 Azure 中的負載平衡器設定。

立即設定負載平衡器 IP 位址。

- 在負載平衡器的 [設定] 刀鋒視窗上，按一下 [IP 位址]。[IP 位址] 刀鋒視窗會顯示這是與您的 SQL Server 位於相同虛擬網路上的私用負載平衡器。 

- 進行下列設定：

| 設定 | 值 |
| ----- | ----- |
| **子網路** | 選擇 SQL Server 所在的子網路。 |
| **指派** | **靜態** |
| **IP 位址** | 輸入子網路中未使用的虛擬 IP 位址。 |

- 儲存設定。

負載平衡器現在便有 IP 位址。請記下此 IP 位址。當您在叢集上建立接聽程式時，您將使用此 IP 位址。在本文稍後的 PowerShell 指令碼中，將此位址用於 `$ILBIP` 變數。



## 2\.設定後端集區

下一個步驟是建立後端位址集區。Azure 會呼叫後端位址集區 *backend pool*。在此情況下，後端集區是您的可用性群組中兩部 SQL Server 的位址。

- 在資源群組中，按一下您建立的負載平衡器。 

- 在 [設定] 上，按一下 [後端集區]。

- 在 [後端位址集區] 上，按一下 [新增] 以建立後端位址集區。

- 在 [新增後端集區] 的 [名稱] 之下，輸入後端集區的名稱。

- 在 [虛擬機器] 之下，按一下 [+ 新增虛擬機器]。

- 在 [選擇虛擬機器] 之下，按一下 [選擇可用性設定組]，然後指定 SQL Server 虛擬機器所屬的可用性設定組。

- 選擇可用性設定組之後，按一下 [選擇虛擬機器]。按一下兩個裝載可用性群組中的 SQL Server 執行個體的虛擬機器。按一下 [選取]。

- 按一下 [確定] 以關閉 [選擇虛擬機器] 和 [新增後端集區] 的刀鋒視窗。

Azure 更新後端位址集區的設定。您的可用性設定組現在有包含兩個 SQL 伺服器的集區。

## 3\.建立探查

下一個步驟是建立探查。探查會定義 Azure 如何確認哪一個 SQL Server 目前擁有可用性群組接聽程式。Azure 會根據在建立探查時定義的連接埠上的 IP 位址來探查服務。

- 在負載平衡器的 [設定] 刀鋒視窗上，按一下 [探查]。 

- 在 [探查] 刀鋒視窗上，按一下 [新增]。

- 在 [新增探查] 刀鋒視窗上設定探查。使用下列值來設定探查：

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 代表探查的文字名稱。例如 **SQLAlwaysOnEndPointProbe**。 |
| **通訊協定** | **TCP** |
| **連接埠** | 您可以使用任何可用的連接埠。例如 *59999*。 |
| **間隔** | *5* | 
| **狀況不良臨界值** | *2* | 

- 按一下 [確定]。 

>[AZURE.NOTE] 確定您指定的連接埠在兩部 SQL Server 在防火牆上開啟。這兩部伺服器需要您使用的 TCP 連接埠的輸入規則。如需詳細資訊，請參閱[新增或編輯防火牆規則](http://technet.microsoft.com/library/cc753558.aspx)。

Azure 會建立探查。Azure 會使用探查來測試那一個 SQL Server 具有可用性群組的接聽程式。

## 4\.設定負載平衡規則

設定負載平衡規則。負載平衡規則會設定負載平衡器將流量路由傳送至 SQL Server 的方式。對此負載平衡器，您將啟用伺服器直接回傳，因為兩部 SQL Server 中一次只有一部會擁有可用性群組接聽程式資源。

- 在負載平衡器的 [設定] 刀鋒視窗上，按一下 [負載平衡規則]。 

- 在 [負載平衡規則] 刀鋒視窗上，按一下 [新增]。

- 使用 [新增負載平衡規則] 刀鋒視窗來設定負載平衡規則。套用下列設定：

| 設定 | 值 |
| ----- | ----- |
| **名稱** | 代表負載平衡規則的文字名稱。例如 **SQLAlwaysOnEndPointListener**。 |
| **通訊協定** | **TCP** |
| **連接埠** | *1433* |
| **後端連接埠** | *1433*。請注意，此設定將停用，因為此規則使用 [浮動 IP (伺服器直接回傳)]。 |
| **探查** | 使用您為此負載平衡器建立之探查的名稱。 |
| **工作階段持續性** | **None** | 
| **閒置逾時 (分鐘)** | *4* | 
| **浮動 IP (伺服器直接回傳)** | **已啟用** | 

 >[AZURE.NOTE] 您可能必須在刀鋒視窗上向下捲動，以查看所有的設定。

- 按一下 [確定]。 

- Azure 會設定負載平衡規則。負載平衡器現已設定成將流量路由傳送到裝載可用性群組接聽程式的 SQL Server。

此時，資源群組有一個連接到這兩部 SQL Server 電腦的負載平衡器。負載平衡器也包含 SQL Server AlwaysOn 可用性群組接聽程式的 IP 位址，以便電腦回應對可用性群組的要求。

>[AZURE.NOTE] 如果您的 SQL Server 位於兩個不同的區域，請在另一個區域中重複執行步驟。每個區域都需要負載平衡器。

## 設定叢集以使用負載平衡器 IP 位址 

下一個步驟是在叢集上設定接聽程式，並且讓接聽程式上線。若要完成這項作業，執行下列步驟：

1. 在容錯移轉叢集上建立可用性群組接聽程式 

1. 使接聽程式上線

## 1\.在容錯移轉叢集上建立可用性群組接聽程式

在此步驟中，您會在容錯移轉叢集管理員和 SQL Server Management Studio (SSMS) 中手動建立可用性群組接聽程式。

- 使用 RDP 連接到裝載主要複本的 Azure 虛擬機器。 

- 開始容錯移轉叢集管理員。

- 選取 [網路] 節點，然後記下叢集網路名稱。PowerShell 指令碼的 `$ClusterNetworkName` 變數中將使用這個名稱。

- 展開叢集名稱，然後按一下 [角色]。

- 在 [角色] 窗格中以滑鼠右鍵按一下可用性群組名稱，然後選取 [加入資源] > [用戶端存取點]。

- 在 [名稱] 方塊中，建立這個新接聽程式的名稱，然後按兩次 [下一步]，再按一下 [完成]。目前請勿讓接聽程式或資源上線工作。

 >[AZURE.NOTE] 新接聽程式的名稱是應用程式將用來連接到 SQL Server 可用性群組中資料庫的網路名稱。

- 按一下 [資源] 索引標籤，然後展開您剛才建立的用戶端存取點。以滑鼠右鍵按一下 IP 資源，然後按一下 [屬性]。請記下 IP 位址的名稱。您將在 PowerShell 指令碼的 `$IPResourceName` 變數中使用這個名稱。

- 按一下 [IP 位址] 之下的 [靜態 IP 位址]，然後將靜態 IP 位址設定為您在 Azure 入口網站上設定負載平衡器 IP 位址時所用的相同位址。針對此位址啟用 NetBIOS，然後按一下 [確定]。如果您的方案跨越多個 Azure VNet，請針對每個 IP 資源重複此步驟。

- 在目前裝載主要複本的叢集節點上，開啟已提升權限的 PowerShell ISE，並將下列命令貼到新的指令碼中。

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- 更新變數並執行 PowerShell 指令碼，以設定新接聽程式的 IP 位址和連接埠。

 >[AZURE.NOTE] 如果您的 SQL Server 位於不同的區域中，您需要執行 PowerShell 指令碼兩次。第一次使用第一個資源群組中的叢集網路名稱、叢集 IP 資源名稱及負載平衡器 IP 位址。第二次使用第二個資源群組中的叢集網路名稱、叢集 IP 資源名稱及負載平衡器 IP 位址。

叢集現在有可用性群組接聽程式資源。

## 2\.使接聽程式上線

設定可用性群組接聽程式資源後，您可以讓接聽程式上線，以便應用程式利用接聽程式連接到可用性群組中的資料庫。

- 瀏覽回到容錯移轉叢集管理員。展開 [角色]，然後反白顯示您的可用性群組。在 [資源] 索引標籤上，於接聽程式名稱上按一下滑鼠右鍵，然後按一下 [屬性]。

- 按一下 [相依性] 索引標籤。如果列出多個資源，請確認 IP 位址具有 OR 相依性，而非 AND。按一下 [確定]。

- 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [線上工作]。


- 一旦接聽程式在線上，從 [資源] 索引標籤，以滑鼠右鍵按一下可用性群組，再按一下 [屬性]。

- 建立對接聽程式名稱資源 (非 IP 位址資源名稱) 的相依性。按一下 [確定]。


- 啟動 SQL Server Management Studio，然後連接到主要複本。


- 瀏覽至 [AlwaysOn 高可用性] | [可用性群組] | [可用性群組接聽程式]。


- 您現在應該會看到在容錯移轉叢集管理員中建立的接聽程式名稱。以滑鼠右鍵按一下接聽程式名稱，然後按一下 [屬性]。


- 在 [連接埠] 方塊中，使用您稍早所用的 $EndpointPort (預設值是 1433) 來指定可用性群組接聽程式的連接埠號碼，然後按一下 [確定]。

在資源管理員模式中執行的 Azure 虛擬機器中，您現在有 SQL Server AlwaysOn 可用性群組。

## 測試接聽程式的連線

若要測試連線︰

1. 透過 RDP 連接到相同虛擬網路中不擁有複本的 SQL Server。這可以是叢集中的其他 SQL Server。

1. 使用 **sqlcmd** 公用程式來測試連線。例如，下列指令碼會透過接聽程式搭配 Windows 驗證來建立主要複本的 **sqlcmd** 連線︰

        sqlmd -S <listenerName> -E

SQLCMD 連線會自動連接到裝載主要複本的 SQL Server 執行個體。

## 指導方針和限制

請注意，下列關於 Azure 中使用內部負載平衡器之可用性群組接聽程式的指導方針：

- 每個雲端服務僅支援一個內部可用性群組接聽程式，因為接聽程式被設定為負載平衡器，而且僅有一個內部負載平衡器。但是可以建立多個外部接聽程式。 

- 使用內部負載平衡器，您只能從相同的虛擬網路內存取接聽程式。
 

<!---HONumber=AcomDC_0601_2016-->