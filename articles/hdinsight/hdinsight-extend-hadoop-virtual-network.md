---
title: "使用虛擬網路延伸 HDInsight | Microsoft Docs"
description: "了解如何使用 Azure 虛擬網路將 HDInsight 連接到其他雲端資源或您的資料中心內的資源"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: afd43fb536278d6aa76acaf7c9d18714bc5acd2b
ms.lasthandoff: 03/15/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>使用 Azure 虛擬網路延伸 HDInsight 功能
Azure 虛擬網路可讓您延伸 Hadoop 解決方案以合併內部部署資源，例如 SQL Server。 它也可讓您結合多個 HDInsight 叢集類型，或在雲端資源間建立安全的私人網路。

## <a name="prerequisites"></a>必要條件

* Azure CLI 2.0：如需詳細資訊，請參閱[安裝和設定 Azure CLI 2.0 (英文)](https://docs.microsoft.com/cli/azure/install-az-cli2)。

* Azure PowerShell：如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

> [!NOTE]
> 需要有最新版的 Azure CLI 和 Azure PowerShell，才能執行本文件中的步驟。 如果您使用較舊的版本，命令可能會不同。 為了獲得最佳結果，請使用先前的連結來安裝最新版本。

## <a id="whatis"></a>什麼是 Azure 虛擬網路？

[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。 虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。
  
    ![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    使用虛擬網路連結 Azure 服務與 Azure HDInsight 可實現下列案例：
  
    * **叫用 HDInsight 服務或工作** 。
    * **直接傳輸資料** 。
    * **結合多個 HDInsight 伺服器** 成為單一方案。 HDInsight 叢集有數種類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。 使用虛擬網路可讓多個叢集彼此直接通訊。

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。
  
    站對站組態可讓您將資料中心的多個資源連接到 Azure 虛擬網路。 您可以使用硬體 VPN 裝置或路由及遠端存取服務進行連接。
  
    ![diagram of site-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。
  
    ![diagram of point-to-site configuration](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    使用虛擬網路連結雲端和資料中心可讓類似案例在僅限雲端的設定上實現。 但若不想受限於使用雲端中的資源，您也可以使用資料中心內的資源。
  
    * **直接傳輸資料** 。 範例是使用 Sqoop 在 SQL Server 往返傳輸資料，或讀取企業營運系統 (LOB) 應用程式所產生的資料。
    * **叫用 HDInsight 服務或工作** 。 範例是使用 HBase Java API 來儲存及擷取 HDInsight HBase 叢集的資料。

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜ [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)＞。

> [!NOTE]
> 建立 Azure 虛擬網路，再佈建 HDInsight 叢集，然後在建立叢集時指定網路。 如需詳細資訊，請參閱＜ [虛擬網路組態工作](https://azure.microsoft.com/documentation/services/virtual-network/)＞。

## <a name="virtual-network-requirements"></a>虛擬網路需求

> [!IMPORTANT]
> 在虛擬網路上建立 HDInsight 叢集需要本節中所述之特定的虛擬網路設定。

### <a name="location-based-virtual-networks"></a>以位置為基礎的虛擬網路

Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。

### <a name="classic-or-v2-virtual-network"></a>傳統或 v2 虛擬網路

以 Linux 為基礎的叢集需要 Azure Resource Manager 虛擬網路 (以 Windows 為基礎的叢集需要傳統虛擬網路)。 如果您沒有正確的網路類型，就無法在建立叢集時加以選取。

您可以加入不同的網路類型，讓您能夠在不相容的虛擬網路上存取資源。 在叢集需要的網路版本中建立叢集，由於這兩個網路會聯結在一起，因此它將能存取另一個網路中的資源。 如需連接傳統和 Resource Manager 虛擬網路的詳細資訊，請參閱[將傳統 VNet 連接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

### <a name="custom-dns"></a>自訂 DNS

建立虛擬網路時，Azure 會為網路中安裝的 Azure 服務 (例如 HDInsight) 提供預設名稱解析。 不過，針對跨網路網域名稱解析之類的情況，您可能就需要使用您自己的「網域名稱系統」(DNS)。 例如，在位於兩個結合的虛擬網路中的服務之間通訊時。 與 Azure 虛擬網路搭配使用時，HDInsight 同時支援預設的 Azure 名稱解析和自訂 DNS。

如需如何使用自訂 DNS 伺服器的詳細資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)文件。

### <a name="secured-virtual-networks"></a>受保護的虛擬網路

HDInsight 服務是受管理服務，在佈建期間和執行時需要有網際網路存取。 Azure 會使用網際網路連線來執行下列動作：

* 監視叢集的健康狀態
* 起始叢集資源的容錯移轉
* 透過調整作業變更叢集中的節點數目
* 其他管理工作

這些作業不需要網際網路的完整存取權。 限制網際網路存取時，允許在連接埠 443 上對下列 IP 位址進行輸入存取。 這樣讓 Azure 能夠管理 HDInsight：

> [!IMPORTANT]
> 應受到允許的 IP 位址是 HDInsight 叢集和「虛擬網路」所在區域特定的 IP 位址。 請使用下列資訊來尋找您所使用區域的 IP 位址。

__巴西南部__區域：

* 191.235.84.104
* 191.235.87.113

__加拿大東部__區域：

* 52.229.127.96
* 52.229.123.172

__加拿大中部__區域：

* 52.228.37.66
* 52.228.45.222

__印度中部__區域：

* 52.172.153.209
* 52.172.152.49

__美國中西部__區域：

* 52.161.23.15
* 52.161.10.167

__美國西部 2__ 區域：

* 52.175.211.210
* 52.175.222.222

__所有其他區域__：

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

允許針對這些位址透過連接埠 443 的輸入存取，可讓您成功將 HDInsight 安裝到安全虛擬網路。

> [!IMPORTANT]
> HDInsight 不支援限制輸出流量，僅限制輸入流量。 針對包含 HDInsight 的子網路定義網路安全性群組規則時，僅使用輸入規則。

下列範例示範如何建立新的網路安全性群組來允許必要的位址，並將安全性群組套用至虛擬網路內的子網路。 修改此範例中使用的 IP 位址，以符合您使用的 Azure 區域。

這些步驟假設您已建立虛擬網路和要安裝 HDInsight 的子網路。 請參閱[使用 Azure 入口網站建立虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

> [!IMPORTANT]
> 請注意這些範例中使用的 `priority` 值。 系統會以 priority 的順序，對網路流量測試規則。 有規則符合測試條件並進而套用時，就不會再測試其他規則。
> 
> 如果您的自訂規則會廣泛封鎖輸入流量 (例如**全部拒絕**規則)，您可能需要調整這些範例中的 priority 值。 範例中的規則必須在封鎖存取的規則之前發生。 否則，會先測試**全部拒絕**規則，而且永遠不會套用此範例中的規則。 您不得封鎖 Azure 虛擬網路的預設規則。 例如，您不應建立套用順序早於預設**允許 VNET 輸入**規則 (優先順序為 65000) 的**全部拒絕**規則。
> 
> 如需網路安全性群組規則的詳細資訊，請參閱[什麼是網路安全性群組？](../virtual-network/virtual-networks-nsg.md)。

**使用 Azure 資源管理範本**

從 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)中使用下列資源管理範本，在具備安全網路組態的 VNet 中建立 HDInsight 叢集：

[部署安全的 Azure VNet 和 VNet 中的 HDInsight Hadoop 叢集](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**使用 Azure PowerShell**

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroup $nsg

**使用 Azure CLI**

1. 使用下列命令來建立名為 `hdisecure`的新網路安全性群組。 使用包含 Azure 虛擬網路的資源群組來取代 **RESOURCEGROUPNAME**。 使用群組建立所在的位置 (區域) 來取代 **LOCATION**。
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    建立群組之後，您會收到新群組的相關資訊。

2. 使用下列將規則加入新的網路安全性群組，這些規則允許從 Azure HDInsight 健全狀況和管理服務透過連接埠 443 的輸入通訊。 將 **RESOURCEGROUPNAME** 取代為包含 Azure 虛擬網路的資源群組名稱。
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. 建立規則之後，請使用下列命令，以擷取此網路安全性群組的唯一識別碼：

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    此命令會傳回類似下列文字的值：

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    如果未獲得預期的結果，請在命令中使用雙引號括住識別碼。

4. 使用下列命令將網路安全性群組套用至子網路。 使用上一個步驟傳回的值取代 __GUID__ 和 __RESOURCEGROUPNAME__ 的值。 使用您建立 HDInsight 叢集時想要使用的虛擬網路名稱和子網路名稱，取代 __VNETNAME__ 和 __SUBNETNAME__。
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    此命令完成之後，即可將 HDInsight 順利安裝至這些步驟所使用子網路上的安全虛擬網路。

> [!IMPORTANT]
> 使用上述步驟只會開放對 Azure 雲端上 HDInsight 健康狀態和管理服務的存取。 任何其他來自虛擬網路外部之對 HDInsight 叢集的存取都會遭到封鎖。 如果想要允許從虛擬網路外部存取，就必須新增額外的網路安全性群組規則。
> 
> 下列範例示範如何啟用從網際網路進行 SSH 存取： 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

如需有關「網路安全性群組」的詳細資訊，請參閱 [網路安全性群組概觀](../virtual-network/virtual-networks-nsg.md)。 如需在 Azure 虛擬網路中控制路由的相關資訊，請參閱[使用者定義的路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。

## <a id="tasks"></a>工作和資訊

本節包含一般工作資訊和搭配使用 HDInsight 與虛擬網路時可能需要的資訊。

### <a name="determine-the-fqdn"></a>確定 FQDN

HDInsight 叢集會獲指派特定的虛擬網路介面完整網域名稱 (FQDN)。 FQDN 是您從虛擬網路中的其他 Azure 資源連接到叢集時應使用的位址。 若要確定 FQDN，請使用下列 URL 來查詢 Ambari 管理服務：

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

您使用叢集名稱及叢集上執行的服務和元件，例如 YARN 資源管理員。

> [!NOTE]
> 傳回的資料是 JavaScript 物件標記法 (JSON) 文件。 若只要擷取 FQDN，您應該使用 JSON 剖析器來擷取 `host_components[0].HostRoles.host_name` 值。

比方說，若要從 HDInsight Hadoop 叢集傳回 FQDN，您可以使用下列其中一種方法，擷取 YARN 資源管理員的資料：

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) 和 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

> [!IMPORTANT]
> 如果您已限制叢集的網際網路存取，就無法透過網際網路使用 Ambari。 相反地，您必須使用下列其中一個方法來擷取 FQDN：
>
> * Azure PowerShell：`Get-AzureRmNetworkInterface -ResourceGroupName GROUPNAME | Foreach-object { Write-Output $_.DnsSettings.InternalFqdn }`
> * Azure CLI 2.0：` az network nic list --resource-group GROUPNAME --query '[].dnsSettings.internalFqdn'`
>
> 在這兩個範例中，使用包含虛擬網路的資源群組名稱來取代 __GROUPNAME__。

### <a name="connecting-to-hbase"></a>連接至 HBase

若要使用 Java API 遠端連接至 HBase，您必須決定 HBase 叢集的 ZooKeeper 仲裁位址，並在應用程式中指定仲裁資訊。

若要取得 ZooKeeper 仲裁位址，請使用下列其中一種方法來查詢 Ambari 管理服務：

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) 和 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> 如需如何搭配使用 Ambari 與 HDInsight 的詳細資訊，請參閱 [使用 Ambari API 監視 HDInsight 中的 Hadoop 叢集](hdinsight-monitor-use-ambari-api.md)。

在擁有仲裁資訊後，請將其用於用戶端應用程式中。

例如，對於使用 HBase API 的 Java 應用程式，您可以將 **hbase-site.xml** 檔案新增至專案，並在此檔案中指定仲裁資訊，如下所示：

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>驗證網路連線

某些服務，例如 SQL Server，可以限制連入的網路連線。 如果您在從 HDInsight 存取服務時遇到問題，請參閱相關服務文件，以確定您已啟用網路存取功能。 您也可以藉由在相同的虛擬網路上建立一台 Azure 虛擬機器來確認網路存取。 接著，使用該虛擬機器上的用戶端公用程式，來確認該虛擬機器可透過虛擬網路連接到服務。

## <a id="nextsteps"></a>接續步驟

下列範例示範如何搭配使用 HDInsight 與 Azure 虛擬網路：

* [使用 HDInsight 中的 Storm 和 HBase 分析感應器資料](hdinsight-storm-sensor-data-analysis.md) - 示範如何在虛擬網路中設定 Storm 和 HBase 叢集。
* [在 HDInsight 佈建 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md) - 提供有關佈建 Hadoop 叢集的資訊，包括有關使用 Azure 虛擬網路的資訊。
* [在 HDInsight 中搭配使用 Sqoop 和 Hadoop](hdinsight-use-sqoop-mac-linux.md) - 提供搭配使用 Sqoop 與 SQL Server 透過虛擬網路傳輸資料的相關資訊。

若要深入了解 Azure 虛擬網路，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。


