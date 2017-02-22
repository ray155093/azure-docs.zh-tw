---
title: "建立 SQL Server 可用性群組接聽程式 - Azure 虛擬機器 | Microsoft Docs"
description: "在 Azure 虛擬機器中建立 SQL Server 的 AlwaysOn 可用性群組接聽程式的逐步指示"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>在 Azure 中設定 Always On 可用性群組的內部負載平衡器
本主題說明如何在 Resource Manager 模型中執行的 Azure 虛擬機器中建立 SQL Server AlwaysOn 可用性群組的內部負載平衡器。 當 SQL Server 執行個體位於 Azure 虛擬機器時，可用性群組需要負載平衡器。 負載平衡器會儲存可用性群組接聽程式的 IP 位址。 如果可用性群組跨越多個區域，則每個區域都需要負載平衡器。

若要完成這項工作，您必須在 Resource Manager 模型中的 Azure 虛擬機器上部署 SQL Server 可用性群組。 這兩部 SQL Server 虛擬機器必須屬於相同的可用性設定組。 您可以使用 [Microsoft 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) 在 Azure Resource Manager 中自動建立可用性群組。 此範本會自動為您建立內部負載平衡器。 

如果您想要的話，也可以 [手動設定可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

本主題會要求您已經設定可用性群組。  

相關主題包括：

* [在 Azure VM 中設定 Always On 可用性群組 (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [使用 Azure Resource Manager 和 PowerShell 來設定 VNet 對 VNet 連線](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

依照這份文件逐步執行，在 Azure 入口網站中建立和設定負載平衡器。 完成之後，您將設定叢集，以將來自負載平衡器的 IP 位址使用於可用性群組接聽程式。

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立及設定負載平衡器
在這部分的工作中，您會在 Azure 入口網站中執行下列步驟︰

1. 建立負載平衡器和設定 IP 位址
2. 設定後端集區
3. 建立探查 
4. 設定負載平衡規則

> [!NOTE]
> 如果 SQL Server 位於不同的資源群組和區域中，您就要進行上述所有步驟兩次，在每個資源群組中各一次。
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1.建立負載平衡器和設定 IP 位址
第一個步驟是建立負載平衡器。 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 在資源群組中，按一下 [新增] 。

1. 搜尋 **負載平衡器**。 從搜尋結果中選取由 **Microsoft** 發行的 [負載平衡器]。
1. 在 [負載平衡器] 刀鋒視窗上，按一下 [建立]。
1. 在 [建立負載平衡器] 上，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表負載平衡器的文字名稱。 例如 **sqlLB**。 |
   | **類型** |**內部** |
   | **虛擬網路** |選擇 SQL Server 所在的虛擬網路。 |
   | **子網路** |選擇 SQL Server 所在的子網路。 |
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** |從子網路指定可用的 IP 位址。 當您在叢集上建立接聽程式時，使用此 IP 位址。 在本文稍後的 PowerShell 指令碼中，將此位址用於 `$ILBIP` 變數。 |
   | **訂用帳戶** |如果您有多個訂用帳戶，此欄位才會出現。 選取您想要與此資源相關聯的訂用帳戶。 通常是與可用性群組的所有資源相同的訂用帳戶。 |
   | **資源群組** |選擇 SQL Server 所在的資源群組。 |
   | **位置** |選擇 SQL Server 所在的 Azure 位置。 |

1. 按一下 [建立] 。 

Azure 會建立負載平衡器。 此負載平衡器屬於特定的網路、子網路、資源群組和位置。 Azure 完成之後，請確認 Azure 中的負載平衡器設定。 

### <a name="2-configure-the-backend-pool"></a>2.設定後端集區
下一個步驟是建立後端位址集區。 Azure 會呼叫後端位址集區 *backend pool*。 在此情況下，後端集區是您的可用性群組中兩部 SQL Server 的位址。 

1. 在資源群組中，按一下您建立的負載平衡器。 
1. 在 [設定] 上，按一下 [後端集區]。
1. 在 [後端集區] 上，按一下 [新增] 以建立後端位址集區。 
1. 在 [建立負載平衡器]  under **名稱**之下，輸入後端集區的名稱。
1. 在 [虛擬機器] 之下，按一下 [+ 新增虛擬機器]。 
1. 在 [選擇虛擬機器] 之下，按一下 [選擇可用性設定組]，然後指定 SQL Server 虛擬機器所屬的可用性設定組。
1. 選擇可用性設定組之後，按一下 [選擇虛擬機器] 。 按一下兩個裝載可用性群組中的 SQL Server 執行個體的虛擬機器。 按一下 [選取] 。 
1. 按一下 [確定] 以關閉 [選擇虛擬機器] 和 [新增後端集區] 的刀鋒視窗。 

Azure 更新後端位址集區的設定。 您的可用性設定組現在有包含兩個 SQL 伺服器的集區。

### <a name="3-create-a-probe"></a>3.建立探查
下一個步驟是建立探查。 探查會定義 Azure 如何確認哪一個 SQL Server 目前擁有可用性群組接聽程式。 Azure 會根據在建立探查時定義的連接埠上的 IP 位址來探查服務。

1. 在負載平衡器的 [設定] 刀鋒視窗上，按一下 [健全狀況探查]。 
1. 在 [健全狀況探查] 刀鋒視窗上，按一下 [新增]。
1. 在 [新增探查]  刀鋒視窗上設定探查。 使用下列值來設定探查：

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表探查的文字名稱。 例如 **SQLAlwaysOnEndPointProbe**。 |
   | **通訊協定** |**TCP** |
   | **連接埠** |您可以使用任何可用的連接埠。 例如 *59999*。 |
   | **間隔** |*5* |
   | **狀況不良臨界值** |*2* |

1.  按一下 [確定] 。 

> [!NOTE]
> 確定您指定的連接埠在兩部 SQL Server 在防火牆上開啟。 這兩部伺服器需要您使用的 TCP 連接埠的輸入規則。 如需詳細資訊，請參閱[新增或編輯防火牆規則](http://technet.microsoft.com/library/cc753558.aspx)。 
> 
> 

Azure 會建立探查。 Azure 會使用探查來測試那一個 SQL Server 具有可用性群組的接聽程式。

### <a name="4-set-the-load-balancing-rules"></a>4.設定負載平衡規則
設定負載平衡規則。 負載平衡規則會設定負載平衡器將流量路由傳送至 SQL Server 的方式。 對此負載平衡器，您將啟用伺服器直接回傳，因為兩部 SQL Server 中一次只有一部會擁有可用性群組接聽程式資源。

1. 在負載平衡器的 [設定] 刀鋒視窗上，按一下 [負載平衡規則]。 
1. 在 [負載平衡規則] 刀鋒視窗上，按一下 [新增]。
1. 使用 [新增負載平衡規則]  刀鋒視窗來設定負載平衡規則。 套用下列設定： 

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |代表負載平衡規則的文字名稱。 例如 **SQLAlwaysOnEndPointListener**。 |
   | **通訊協定** |**TCP** |
   | **連接埠** |*1433* |
   | **後端連接埠** |*1433*.將忽略此值，因為此規則使用 [浮動 IP (伺服器直接回傳)]。 |
   | **探查** |使用您為此負載平衡器建立之探查的名稱。 |
   | **工作階段持續性** |**None** |
   | **閒置逾時 (分鐘)** |*4* |
   | **浮動 IP (伺服器直接回傳)** |**已啟用** |

   > [!NOTE]
   > 您可能必須在刀鋒視窗上向下捲動，以查看所有的設定。
   > 

1. 按一下 [確定] 。 
1. Azure 會設定負載平衡規則。 負載平衡器現已設定成將流量路由傳送到裝載可用性群組接聽程式的 SQL Server。 

此時，資源群組有一個連接到這兩部 SQL Server 電腦的負載平衡器。 負載平衡器也包含 SQL Server AlwaysOn 可用性群組接聽程式的 IP 位址，以便電腦回應對可用性群組的要求。

> [!NOTE]
> 如果您的 SQL Server 位於兩個不同的區域，請在另一個區域中重複執行步驟。 每個區域都需要負載平衡器。 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>設定叢集以使用負載平衡器 IP 位址
下一個步驟是在叢集上設定接聽程式，並且讓接聽程式上線。 請執行下列步驟： 

1. 在容錯移轉叢集上建立可用性群組接聽程式 
2. 使接聽程式上線

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5.在容錯移轉叢集上建立可用性群組接聽程式
在此步驟中，您會在容錯移轉叢集管理員和 SQL Server Management Studio (SSMS) 中手動建立可用性群組接聽程式。

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>驗證接聽程式的組態

如果已正確設定叢集資源和相依性，您應該能夠看到 SQL Server Management Studio 中的接聽程式。 執行下列步驟來設定接聽程式連接埠︰

1. 啟動 SQL Server Management Studio，然後連接到主要複本。
2. 瀏覽至 [AlwaysOn 高可用性] | [可用性群組] | [可用性群組接聽程式]。 
1. 您現在應該會看到在容錯移轉叢集管理員中建立的接聽程式名稱。 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [屬性] 。
1. 在 [連接埠] 方塊中，使用您稍早所用的 $EndpointPort (預設值是&1433;) 來指定可用性群組接聽程式的連接埠號碼，然後按一下 [確定]。

在 Resource Manager 模式中執行的 Azure 虛擬機器中，您現在有 SQL Server AlwaysOn 可用性群組。 

## <a name="test-the-connection-to-the-listener"></a>測試接聽程式的連線
若要測試連線︰

1. 透過 RDP 連接到相同虛擬網路中不擁有複本的 SQL Server。 這可以是叢集中的其他 SQL Server。
2. 使用 **sqlcmd** 公用程式來測試連線。 例如，下列指令碼會透過接聽程式搭配 Windows 驗證來建立主要複本的 **sqlcmd** 連線︰
   
        sqlcmd -S <listenerName> -E

SQLCMD 連線會自動連線到任何一個裝載主要複本的 SQL Server 執行個體。 

## <a name="guidelines-and-limitations"></a>指導方針和限制
請注意，下列關於 Azure 中使用內部負載平衡器之可用性群組接聽程式的指導方針：

* 每個雲端服務僅支援一個內部可用性群組接聽程式，因為接聽程式被設定為負載平衡器，而內部負載平衡器只有一個。 但是可以建立多個外部接聽程式。 
* 使用內部負載平衡器，您只能從相同的虛擬網路內存取接聽程式。


<!--HONumber=Jan17_HO2-->


