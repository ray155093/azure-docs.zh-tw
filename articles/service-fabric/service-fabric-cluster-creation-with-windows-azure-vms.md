<properties
   pageTitle="建立具有執行 Windows 之 Azure VM 的獨立叢集 | Microsoft Azure"
   description="了解如何在執行 Windows Server 的 Azure 虛擬機器上建立和管理 Azure Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# 建立三個具有執行 Windows Server 之 Azure 虛擬機器的節點獨立 Service Fabric 叢集

本文說明如何使用適用於 Windows Server 的獨立 Service Fabric 安裝程式在 Windows 架構的 Azure 虛擬機器 (VM) 上建立叢集。這是[建立和管理在 Windows Server 上執行的叢集](service-fabric-cluster-creation-for-windows-server.md)的特殊案例，其 VM 是[執行 Windows Server 的 Azure VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)，但您不是在建立 [Azure 雲端型 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)。其差別是透過下列步驟所建立的獨立 Service Fabric 叢集是完全由您管理，而 Azure 雲端型 Service Fabric 叢集則是由 Service Fabric 資源提供者來管理和升級。


## 獨立叢集的建立步驟

1. 登入 Azure 入口網站，並在資源群組中建立新的 Windows Server 2012 R2 Datacenter VM。如需詳細資訊，請閱讀[在 Azure 入口網站中建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)一文。
2. 再新增兩個 Windows Server 2012 R2 Datacenter VM 至相同的資源群組。確定每個 VM 在建立時具有相同的系統管理員使用者名稱和密碼。一旦建立好，您應該會在相同的虛擬網路中看到這三個 VM。
3. 使用 [[伺服器管理員] > [本機伺服器]](https://technet.microsoft.com/library/jj134147.aspx) 儀表板連接到每個 VM 並關閉 Windows 防火牆。這可確保網路流量可在電腦之間進行通訊。在連接到每個電腦時，透過開啟命令提示字元並輸入 `ipconfig`來取得 IP 位址。或者，您可以在 Azure 入口網站中選取資源群組的虛擬網路資源，以查看每部電腦的 IP 位址。
4. 連接到其中一個 VM，並測試您是否可以成功 ping 到其他兩個 VM。
5. 連接到其中一個 VM，並[下載適用於 Windows Server 的獨立 Service Fabric 封裝](http://go.microsoft.com/fwlink/?LinkId=730690)到電腦上的新資料夾，然後解壓縮封裝。
6. 在記事本開啟「ClusterConfig.Unsecure.MultiMachine.json」檔案，然後使用電腦的三個 IP 位址編輯每個節點。在頂端變更叢集名稱，然後儲存檔案。叢集資訊清單的部分範例如下所示。

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. 開啟 [PowerShell ISE 視窗](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。瀏覽至所下載獨立安裝程式封裝的解壓縮資料夾，然後儲存叢集資訊清單檔案。執行下列 PowerShell 命令。

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. 您應該會看到 PowerShell 執行、連接到每部電腦並建立叢集。大約 1 分鐘過後，您就可以透過在其中一個電腦 IP 位址 (例如使用 `http://10.7.0.5:19080/Explorer/index.html`) 連接到 Service Fabric Explorer，檢查叢集是否已運作。由於這是使用 Azure VM 的獨立叢集，如果您想要保護其安全，就必須[將憑證部署至 Azure VM](service-fabric-windows-cluster-x509-security.md)，或將其中一部電腦設定為[用來進行 Windows 驗證的 Windows Server Active Directory (AD) 控制站](service-fabric-windows-cluster-windows-security.md)，就像您在內部部署所做的一樣。


## 後續步驟
- [在 Windows Server 或 Linux 上建立獨立的 Service Fabric 叢集](service-fabric-deploy-anywhere.md)
- [在獨立 Service Fabric 叢集中新增或移除節點](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)
- [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)
- [使用 X509 憑證保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0810_2016---->