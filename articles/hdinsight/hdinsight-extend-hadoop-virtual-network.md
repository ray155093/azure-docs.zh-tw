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
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a3052e53c642ef3e6d9bb0489476274987707f91
ms.lasthandoff: 04/27/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>使用 Azure 虛擬網路延伸 HDInsight 功能

了解如何使用 Azure 虛擬網路搭配 HDInsight 來實現下列案例：

* 限制對 HDInsight 的存取。 例如，防止來自網際網路的輸入流量。

* 直接存取 HDInsight 上不透過網際網路公開的服務。 例如，直接使用 Kafka 代理程式或使用 HBase Java API。

* 直接將服務連至 HDInsight。 例如，使用 Oozie 將您資料中心內 SQL Server 的資料匯入或匯出。

* 建立包含多個 HDInsight 叢集的方案。 例如，使用 Spark 或 Storm 來分析儲存在 Kafka 中的資料。

## <a name="prerequisites"></a>必要條件

* Azure CLI 2.0：如需詳細資訊，請參閱[安裝和設定 Azure CLI 2.0 (英文)](https://docs.microsoft.com/cli/azure/install-az-cli2)。

* Azure PowerShell：如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/overview)。

> [!NOTE]
> 需要有最新版的 Azure CLI 和 Azure PowerShell，才能執行本文件中的步驟。 如果您使用較舊的版本，命令可能會不同。 為了獲得最佳結果，請使用先前的連結來安裝最新版本。

## <a id="whatis"></a>什麼是 Azure 虛擬網路

[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。

以下是在虛擬網路中使用 HDInsight 時的考量清單：

* __傳統和 Resource Manager 虛擬網路__：根據 HDInsight 叢集作業系統，使用下表來判斷要使用的網路類型：

    | HDInsight 作業系統 | 傳統虛擬網路 | Resource Manager 虛擬網路 |
    | ---- | ---- | ---- |
    | Linux | no | yes |
    | Windows | yes | no |

    若要在不相容的虛擬網路中存取資源，請聯結兩個網路。 如需連接傳統和 Resource Manager 虛擬網路的詳細資訊，請參閱[將傳統 VNet 連接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

* __自訂 DNS__：Azure 會針對安裝於 Azure 虛擬網路中的 Azure 服務提供名稱解析。 此名稱解析不會延伸到虛擬網路外。 若要針對虛擬網路以外的資源啟用名稱解析，您必須使用自訂 DNS 伺服器。 如需如何使用自訂 DNS 伺服器的詳細資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)文件。

* __強制通道__：HDInsight 不支援 Azure 虛擬網路的強制通道設定。

* __限制網路流量__：HDInsight 支援使用網路安全性群組限制網路流量，但需要無限制地存取數個 Azure IP。 如需詳細資訊，請參閱[受保護的虛擬網路](#secured-virtual-networks)一節。

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>在私人網路中將雲端資源連接在一起 (僅限雲端)

![diagram of cloud-only configuration](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

使用虛擬網路連結 Azure 服務與 Azure HDInsight 可實現下列案例：

* **叫用 HDInsight 服務或工作** 。

* **直接傳輸資料** 。

* **結合多個 HDInsight 伺服器** 成為單一方案。 HDInsight 叢集有數種類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。 使用虛擬網路可讓多個叢集彼此直接通訊。

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>將雲端資源連接到本機資料中心網路

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

## <a name="secured-virtual-networks"></a>受保護的虛擬網路

HDInsight 服務是受管理的服務，在佈建期間和執行時都需要存取 Azure 管理服務。 Azure 管理會執行下列服務：

* 監視叢集的健康狀態
* 起始叢集資源的容錯移轉
* 透過調整作業變更叢集中的節點數目
* 其他管理工作

> [!NOTE]
> 這些作業不需要網際網路的完整存取權。 限制網際網路存取時，請允許在連接埠 443 上對下列 IP 位址進行輸入存取。 這樣讓 Azure 能夠管理 HDInsight：

應受到允許的 IP 位址是 HDInsight 叢集和「虛擬網路」所在區域特定的 IP 位址。 請使用下表來尋找您所使用區域的 IP 位址。

| 國家 (地區) | 區域 | 允許的 IP 位址 | 允許的連接埠 |
| ---- | ---- | ---- | ---- |
| 巴西 | 巴西南部 | 191.235.84.104</br>191.235.87.113 | 443 |
| 加拿大 | 加拿大東部 | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | 加拿大中部 | 52.228.37.66</br>52.228.45.222 | 443 |
| 德國 | 德國中部 | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | 德國東北部 | 51.5.150.132</br>51.5.144.101 | 443 |
| 印度 | 印度中部 | 52.172.153.209</br>52.172.152.49 | 443 |
| 英國 | 英國西部 | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | 英國南部 | 51.140.47.39</br>51.140.52.16 | 443 |
| 美國 | 美國中西部 | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | 美國西部 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__如果您的區域未列在表中__，請允許下列 IP 位址對連接埠 __443__ 的流量：

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight 不支援限制輸出流量，僅限制輸入流量。 針對包含 HDInsight 的子網路定義網路安全性群組規則時，__僅使用輸入規則__。

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>在受保護的虛擬網路中使用 HDInsight

如果您封鎖網際網路存取，您就無法使用通常會透過叢集公用閘道公開的 HDInsight 服務。 這些服務包括 Ambari 和 SSH。 您必須改為使用叢集前端節點的內部 IP 位址來存取服務。

若要尋找前端節點的內部 IP 位址，請使用[內部 IP 與 FQDN](#internal-ips-and-fqdns) 一節中的指令碼。

### <a name="example-secured-virtual-network"></a>範例：受保護的虛擬網路

下列範例示範如何建立網路安全性群組，它允許以下 IP 位址針對連接埠 443 的輸入流量：

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> 這些位址適用於未列出具有特定 IP 位址的地區。 若要尋找適用於您地區的 IP 位址，請使用[受保護的虛擬網路](#secured-virtual-networks)一節中的資訊。

這些步驟假設您已建立虛擬網路和要安裝 HDInsight 的子網路。 請參閱[使用 Azure 入口網站建立虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

> [!WARNING]
> 系統會以 __priority__ 的順序，對網路流量測試規則。 一旦規則符合測試條件，就會套用規則，並且不再為該要求測試其他規則。 如果您有規則大範圍地封鎖了輸入流量 (例如「全部拒絕」規則)，則它__必須__在允許流量的規則之後。
>
> 如需網路安全性群組規則的詳細資訊，請參閱[什麼是網路安全性群組](../virtual-network/virtual-networks-nsg.md)文件。

**範例：Azure 資源管理範本**

從 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)中使用下列資源管理範本，在具備安全網路組態的 VNet 中建立 HDInsight 叢集：

[部署安全的 Azure VNet 和 VNet 中的 HDInsight Hadoop 叢集](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**範例：Azure PowerShell**

```powershell
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
# Create a Network Security Group.
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
```

**範例：Azure CLI**

1. 使用下列命令來建立名為 `hdisecure`的新網路安全性群組。 使用包含 Azure 虛擬網路的資源群組來取代 **RESOURCEGROUPNAME**。 使用群組建立所在的位置 (區域) 來取代 **LOCATION**。

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    建立群組之後，您會收到新群組的相關資訊。

2. 使用下列將規則新增至新的網路安全性群組，這些規則允許從 Azure HDInsight 健康狀態和管理服務透過連接埠 443 的輸入通訊。 將 **RESOURCEGROUPNAME** 取代為包含 Azure 虛擬網路的資源群組名稱。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. 建立規則之後，請使用下列命令，以擷取此網路安全性群組的唯一識別碼：

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    此命令會傳回類似下列文字的值：

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    如果未獲得預期的結果，請在命令中使用雙引號括住識別碼。

4. 使用下列命令將網路安全性群組套用至子網路。 使用上一個步驟傳回的值取代 __GUID__ 和 __RESOURCEGROUPNAME__ 的值。 使用您建立 HDInsight 叢集時想要使用的虛擬網路名稱和子網路名稱，取代 __VNETNAME__ 和 __SUBNETNAME__。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    此命令完成之後，即可將 HDInsight 順利安裝至這些步驟所使用子網路上的安全虛擬網路。

> [!IMPORTANT]
> 使用上述步驟只會開放對 Azure 雲端上 HDInsight 健康狀態和管理服務的存取。 任何其他來自虛擬網路外部之對 HDInsight 叢集的存取都會遭到封鎖。 若要允許從外部虛擬網路存取，您必須新增額外的網路安全性群組規則。
>
> 下列範例示範如何啟用從網際網路進行 SSH 存取：
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

如需有關「網路安全性群組」的詳細資訊，請參閱 [網路安全性群組概觀](../virtual-network/virtual-networks-nsg.md)。 如需在 Azure 虛擬網路中控制路由的相關資訊，請參閱[使用者定義的路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="retrieve-internal-ips-and-fqdns"></a>擷取內部 IP 和 FQDN

當使用虛擬網路連線到 HDInsight 時，您可以直接連線到叢集中的端點。 使用下列指令碼來判斷叢集中端點的內部 IP 位址和完整網域名稱 (FQDN)：

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__Azure CLI__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> 在 Azure CLI 2.0 範例中，將 `<resourcegroupname>` 取代為包含虛擬網路的資源群組名稱。

指令碼透過查詢叢集的虛擬網路介面卡 (NIC) 來運作。 NIC 存在於資源群組中，該資源群組包含 HDInsight 所使用的虛擬網路。

## <a id="nextsteps"></a>接續步驟

下列範例示範如何搭配使用 HDInsight 與 Azure 虛擬網路：

* [Azure 虛擬網路中的 HBase 叢集](hdinsight-hbase-provision-vnet.md)

* [在 HDInsight 中使用 Storm 和 HBase 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)

* [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)

* [搭配使用 Sqoop 與 HDInsight 中的 Hadoop](hdinsight-use-sqoop-mac-linux.md)

若要深入了解 Azure 虛擬網路，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。


