---
title: "使用虛擬網路連線到 HDInsight 上的 Kafka - Azure | Microsoft Docs"
description: "了解如何使用 kafka-python 用戶端從遠端連線到 HDInsight 上的 Kafka。 本文件中的組態使用 Azure 虛擬網路內的 HDInsight。 遠端用戶端會透過點對站 VPN 閘道連線到虛擬網路。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9489fdc3c5388a7510bc4411b4abb05fa72fbf4f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/08/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>透過 Azure 虛擬網路連線到 HDInsight (預覽版) 上的 Kafka

了解如何使用 Azure 虛擬網路連線到 HDInsight 上的 Kafka。 Kafka 用戶端 (產生者和消費者) 可以直接在 HDInsight 或遠端系統上任一者執行。 遠端用戶端必須透過 Azure 虛擬網路才能連線到 HDInsight 上的 Kafka。 使用本文件的資訊可了解遠端用戶端如何使用 Azure 虛擬網路連線到 HDInsight。

> [!IMPORTANT]
> 本文件中所討論的幾項組態可用於 Windows、macOS 或 Linux 用戶端。 不過包含的點對站範例只提供 Windows 的 VPN 用戶端。
>
> 此範例也使用 Python 用戶端 ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) 來驗證與 HDInsight 上 Kafka 的通訊。

## <a name="architecture-and-planning"></a>架構與規劃

HDInsight 叢集在 Azure 虛擬網路內會受到保護，而且只允許連入的 SSH 和 HTTPS 流量。 流量會透過公用閘道而來，不會從 Kafka 用戶端路由流量。 若要從遠端用戶端存取 Kafka，您必須建立 Azure 虛擬網路，提供虛擬私人網路 (VPN) 閘道。 設定好虛擬網路和閘道之後，請將 HDInsight 安裝到虛擬網路，並使用 VPN 閘道與它連線。

![Azure 虛擬網路內的 HDInsight 透過 VPN 與用戶端連線的圖表](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

下列清單包含使用 HDInsight 上的 Kafka 搭配虛擬網路之程序的相關資訊︰

1. 建立虛擬網路。 如需使用 HDInsight 搭配 Azure 虛擬網路的特定資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](hdinsight-extend-hadoop-virtual-network.md)文件。

2. (選擇性) 在虛擬網路內建立 Azure 虛擬機器，並在上面安裝自訂的 DNS 伺服器。 此 DNS 伺服器用來啟用站對站或 vnet 對 vnet 組態中遠端用戶端的名稱解析。 如需詳細資訊，請參閱 [VM 與雲端服務的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文件。

3. 建立虛擬網路的 VPN 閘道。 如需有關 VPN 閘道組態的詳細資訊，請參閱[關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)文件。

4. 在虛擬網路內建立 HDInsight。 如果您已設定網路的自訂 DNS 伺服器，HDInsight 會自動設定來使用它。

5. (選擇性) 如果您未使用自訂的 DNS 伺服器，而且沒有用戶端和虛擬網路之間的名稱解析，您就必須設定 Kafka 進行 IP 公告。 如需詳細資訊，請參閱本文件的[設定 Kafka 進行 IP 公告](#configure-kafka-for-ip-advertising)一節。

## <a name="create-using-powershell"></a>建立：使用 PowerShell

本節中的步驟會使用 [Azure PowerShell](/powershell/azure/overview) 建立下列組態：

* Azure 虛擬網路
* 點對站 VPN 閘道
* Azure 儲存體帳戶 (供 HDInsight 使用)
* HDInsight 上的 Kafka

1. 請依照[使用點對站連線的自我簽署憑證](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)文件中的步驟來建立閘道所需的憑證。

2. 開啟 PowerShell 提示字元，並使用下列程式碼來登入您的 Azure 訂用帳戶︰

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. 使用下列程式碼來建立包含組態資訊的變數︰

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. 使用下列程式碼來建立 Azure 資源群組和虛擬網路︰

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > 此程序可能需要幾分鐘的時間才能完成。

5. 使用下列程式碼來建立 Azure 儲存體帳戶和 Blob 容器：

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 使用下列程式碼來建立 HDInsight 叢集︰

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > 此程序大約需要 20 分鐘才能完成。

8. 使用下列 Cmdlet 來擷取虛擬網路之 Windows VPN 用戶端的 URL：

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    若要下載 Windows VPN 用戶端，請使用網頁瀏覽器中傳回的 URI。

## <a name="configure-kafka-for-ip-advertising"></a>設定 Kafka 進行 IP 公告

Zookeeper 預設會將 Kafka 代理程式的網域名稱傳回給用戶端。 這個組態不適用於 VPN 用戶端，因為它無法為虛擬網路中的實體使用名稱解析。 使用下列步驟來設定 HDInsight 上的 Kafka 公告 IP 位址而不是網域名稱︰

1. 使用網頁瀏覽器移至 https://CLUSTERNAME.azurehdinsight.net。 將 __CLUSTERNAME__ 取代為 HDInsight 叢集上 Kafka 的名稱。

    出現提示時，請使用叢集的 HTTPS 使用者名稱和密碼。 此時會顯示叢集的 Ambari Web UI。

2. 若要檢視 Kafka 上的資訊，請從左邊的清單選取 [Kafka]。

    ![反白顯示 Kafka 的服務清單](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. 若要檢視 Kafka 組態，請從正上方選取 [Configs (設定)]。

    ![Kafka 的 Configs (設定) 連結](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. 若要找出 __kafka-env__ 組態，請在右上角的 [Filter (篩選)] 欄位中輸入 `kafka-env`。

    ![Kafka 組態，找出 kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. 若要設定 Kafka 公告 IP 位址，請在 [kafka-env-template] 欄位的底部加入下列文字︰

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. 若要設定 Kafka 接聽的介面，請在右上角的 [Filter (篩選)] 欄位中輸入 `listeners`。

7. 若要設定 Kafka 在所有網路介面上接聽，請將 [listeners (接聽程式)] 欄位的值變更為 `PLAINTEXT://0.0.0.0:92092`。

8. 若要儲存組態變更，請使用 [Save (儲存)] 按鈕。 輸入描述變更的文字訊息。 儲存變更後，請選取 [OK (確定)]。

    ![儲存組態按鈕](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. 若要避免重新啟動 Kafka 時發生錯誤，請使用 [Service Actions (服務動作)] 按鈕，然後選取 [Turn On Maintenance Mode (開啟維護模式)]。 選取 [OK (確定)] 以完成此作業。

    ![服務動作，反白顯示開啟維護](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. 若要重新啟動 Kafka，請使用 [Restart (重新啟動)] 按鈕，然後選取 [Restart All Affected (重新啟動所有受影響項目)]。 確認重新啟動，然後在作業完成之後使用 [OK (確定)] 按鈕。

    ![重新啟動按鈕，反白顯示重新啟動所有受影響項目](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. 若要停用維護模式，請使用 [Service Actions (服務動作)] 按鈕，然後選取 [Turn Off Maintenance Mode (關閉維護模式)]。 選取 [OK (確定)] 以完成此作業。

## <a name="connect-to-the-vpn-gateway"></a>連線到 VPN 閘道

若要從 __Windows 用戶端__連線到 VPN 閘道，請使用[設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameclientcertificatea7---install-an-exported-client-certificate)文件的＜連線到 Azure＞一節。

## <a name="remote-kafka-client"></a>遠端 Kafka 用戶端

若要從用戶端電腦連線到 Kafka，您必須使用 Kafka 代理程式或 Zookeeper 節點 (視您的用戶端需要) 的 IP 位址。 使用下列步驟來擷取 Kafka 代理程式的 IP 位址，然後從 Python 應用程式使用這些位址。

1. 使用下列指令碼來擷取叢集中節點的 IP 位址︰

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    這個指令碼假設 `$resourceGroupName` 是包含虛擬網路的 Azure 資源群組名稱。 指令碼的輸出類似下列文字：

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > 如果您的 Kafka 用戶端使用 Zookeeper 節點而不是 Kafka 代理程式，請在 PowerShell 指令碼中將 `*workernode*` 取代為 `*zookeepernode*`。

    > [!WARNING]
    > 如果您調整叢集大小，或者節點失敗並被取代，IP 位址可能會變更。 目前沒有辦法為 HDInsight 叢集中的節點預先指派特定的 IP 位址。

2. 使用下列命令來安裝 [kafka-python](http://kafka-python.readthedocs.io/) 用戶端︰

        pip install kafka-python

3. 若要將資料傳送至 Kafka，請使用下列 Python 程式碼︰

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    將 `'ip_address'` 項目取代為從本節的步驟 1 傳回的位址。

    > [!NOTE]
    > 此程式碼會將 `test message` 字串傳送至 `testtopic` 主題。 HDInsight 上 Kafka 的預設組態是建立主題 (如果不存在)。

4. 若要從 Kafka 擷取訊息，請使用下列 Python 程式碼︰

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    將 `'ip_address'` 項目取代為從本節的步驟 1 傳回的位址。 輸出包含在上一個步驟中傳送到產生者的測試訊息。

## <a name="troubleshooting"></a>疑難排解

如果您無法連線到虛擬網路，或無法透過網路連線到 HDInsight，請參閱[對虛擬網路閘道器與連線進行疑難排解](../network-watcher/network-watcher-troubleshoot-manage-powershell.md)文件中的指引。

## <a name="next-steps"></a>後續步驟

如需如何建立具點對站 VPN 閘道之 Azure 虛擬網路的詳細資訊，請參閱下列文件︰

* [使用 Azure 入口網站設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [使用 Azure PowerShell 設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

如需使用 HDInsight 上 Kafka 的詳細資訊，請參閱下列文件：

* [開始使用 HDInsight 上的 Kafka](hdinsight-apache-kafka-get-started.md)
* [對 HDInsight 上的 Kafka 使用鏡像](hdinsight-apache-kafka-mirroring.md)

