<properties
   pageTitle="建立內部部署或任何雲端 Azure Service Fabric 叢集 | Microsoft Azure"
   description="了解如何在執行 Windows Server (無論是在內部部署或任何雲端) 的任何電腦 (實體或虛擬) 上建立 Azure Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# 建立在 Windows Server 上執行的叢集

Azure Service Fabric 可允許在執行 Windows Server 的任何虛擬機器或電腦上建立 Service Fabric 叢集。這表示您能夠在任何環境中，不論是在內部部署環境或是透過任何雲端提供者，只要有一組互連式 Windows Server 電腦，都可部署和執行 Service Fabric 應用程式。Service Fabric 會提供一個安裝封裝供您建立稱為獨立 Windows Server 封裝的 Service Fabric 叢集。

本文將逐步引導您使用 Service Fabric 內部部署 (但可針對其他任何環境輕鬆地調整，例如其他雲端提供者) 的獨立封裝完成建立叢集的步驟。

>[AZURE.NOTE] 此獨立 Windows Server 封裝目前為預覽版本，並不支援生產環境工作負載。如果您想要立即下載一份 EULA，[按一下這裡](http://go.microsoft.com/fwlink/?LinkID=733084)。

<a id="downloadpackage"></a>
## 下載 Service Fabric 獨立封裝


[下載適用於 Windows Server 2012 R2 和更新版本的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)，名稱為「Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip」。

在下載套件中，您將會找到下列檔案︰

|**檔案名稱**|**簡短說明**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|CAB 檔，其中包含部署到叢集中每部電腦的二進位檔。|
|ClusterConfig.Unsecure.DevCluster.json|叢集組態範例檔案，其中包含適用於不安全、三個節點、單一 VM/電腦部署叢集的設定，包括叢集中每個節點的相關資訊。 |
|ClusterConfig.Unsecure.MultiMachine.json|叢集組態範例檔案，其中包含適用於不安全、多個 VM/電腦叢集的設定，包括叢集中每個電腦的相關資訊。|
|ClusterConfig.Windows.DevCluster.json|叢集組態範例檔案，其中包含適用於安全、三個節點、單一 VM/電腦部署叢集的所有設定，包括叢集中每個節點的相關資訊。使用 [Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護叢集的安全。|
|ClusterConfig.Windows.MultiMachine.json|叢集組態範例檔案，其中包含適用於使用 Windows 安全性的安全、多個 VM/電腦叢集的所有設定，包括安全叢集中每個電腦的相關資訊。使用 [Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護叢集的安全。|
|ClusterConfig.x509.DevCluster.json|叢集組態範例檔案，其中包含適用於安全、三個節點、單一 VM/電腦部署叢集的所有設定，包括叢集中每個節點的相關資訊。使用 x509 憑證保護叢集的安全。|
|ClusterConfig.x509.MultiMachine.json|叢集組態範例檔案，其中包含適用於安全、多個 VM/電腦叢集的所有設定，包括安全叢集中每個節點的相關資訊。使用 x509 憑證保護叢集的安全。|
|EULA.txt|使用 Microsoft Azure Service Fabric 獨立 Windows Server 封裝的授權條款。如果您想要立即下載一份 EULA，[按一下這裡](http://go.microsoft.com/fwlink/?LinkID=733084)。|
|Readme.txt|指向版本資訊和基本安裝指示的連結。這是您將會在此頁面上找到的一部分指示。|
|CreateServiceFabricCluster.ps1|使用 ClusterConfig.json 檔案中的設定建立叢集的 PowerShell 指令碼。|
|RemoveServiceFabricCluster.ps1|使用 ClusterConfig.json 中的設定移除叢集的 PowerShell 指令碼。|
|AddNode.ps1|用於將節點加入至現有部署叢集的 PowerShell 指令碼。|
|RemoveNode.ps1|用於在現有部署叢集移除節點的 PowerShell 指令碼。|


## 規劃及準備叢集部署
建立叢集之前，必須先執行下列步驟。

### 步驟 1︰規劃叢集基礎結構
您即將在您所擁有的電腦上建立 Service Fabric 叢集，因此您可以決定您希望叢集不受何種失敗的影響。例如，您是否需要提供這些電腦的個別電源線或網際網路連線？ 此外，您也應該考慮這些電腦的實體安全性。實體位置在哪裡？誰需要存取這些電腦？ 一旦您做出這些決定之後，您要以邏輯的方式，將電腦對應到多個容錯網域 (如需詳細資訊，請參閱下方)。相較於測試叢集，生產叢集的基礎結構規劃更為複雜。

### 步驟 2︰準備符合必要條件的電腦
您想要新增到叢集的每部電腦的必要條件︰

- 建議至少有 2 GB 的記憶體
- 網路連線。請確定電腦位於一或多個安全的網路
- Windows Server 2012 R2 或 Windows Server 2012 (您必須為此系統安裝 KB2858668)。
- .NET Framework 4.5.1 或更高版本，完整安裝
- Windows PowerShell 3.0
- 部署和設定叢集的叢集系統管理員必須擁有每部電腦的系統管理員權限。
- RemoteRegistry 服務應該在所有電腦上執行。

### 步驟 3︰決定初始叢集大小
每個節點都已部署 Service Fabric 執行階段，並且是叢集的成員。在一般生產部署中，每個作業系統執行個體 (實體或虛擬) 都有一個節點。叢集大小取決於您的業務需求。不過，您必須至少有三個節點 (電腦/VM) 的叢集大小。請注意，基於開發目的，在一部指定的電腦上可以有多個節點。在生產環境中，對於每個實體或虛擬機器，Service Fabric 只支援一個節點。

### 步驟 4︰決定容錯網域和升級網域的數目
**容錯網域 (FD)** 是故障的實體單元，而且與資料中心內的實體基礎結構直接相關。容錯網域是由共用單一失敗點的硬體元件 (電腦、交換器、網路等) 所組成。雖然容錯網域和機架之間沒有 1:1 對應，但是大致上來說，可以將每個機架視為一個容錯網域。考慮叢集中的節點時，強烈建議至少在三個容錯網域之間散佈節點。

當您在「ClusterConfig.json」中指定 FD 時，可以選擇每個 FD 的名稱。Service Fabric 支援階層式 FD，因此，您可以在 FD 中反映您的基礎結構拓撲。例如，下列 FD 有效：

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**升級網域 (UD)** 是節點的邏輯單元。在 Service Fabric 協調式升級 (應用程式升級或叢集升級) 期間，會關閉 UD 中的所有節點來執行升級，而其他 UD 中的節點則仍然可用來提供要求。您對電腦所進行的韌體升級將不會接受 UD，因此您必須一次在一部電腦上進行。

思考這些概念最簡單的方式就是將 FD 視為非計劃性故障的單元，並將 UD 視為計劃性維護的單元。

當您在「ClusterConfig.json」中指定 UD 時，可以選擇每個 UD 的名稱。例如，下列名稱有效：

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

如需升級網域和容錯網域的詳細資訊，請閱讀[描述 Service Fabric 叢集](service-fabric-cluster-resource-manager-cluster-description.md)一文。

### 步驟 5︰下載適用於 Windows Server 的 Service Fabric 獨立封裝
[下載適用於 Windows Server 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)，並將封裝解壓縮至不屬於叢集一部分的部署電腦，或解壓縮至將所屬於叢集的其中一部電腦。

<a id="createcluster"></a>
## 建立叢集

完成以上規劃和準備一節中所述的步驟之後，您現在就可以開始建立您的叢集。

### 步驟 1︰修改叢集組態
「ClusterConfig.json」檔案中會描述叢集。如需此檔案中各個區段的詳細資訊，請閱讀[獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)一文。從您下載的封裝中開啟其中一個「ClusterConfig.json」檔案，然後修改下列設定︰

|**組態設定**|**說明**|
|-----------------------|--------------------------|
|**NodeTypes**|節點類型可讓您將叢集節點分成不同的群組。一個叢集至少必須有一個節點類型。群組中的所有節點都有下列共同的特性。<br> **Name** - 這是節點類型名稱。<br>**Endpoints Ports** - 這些是與這個節點類型相關聯的各種具名端點 (連接埠)。您可以使用任何您希望的連接埠號碼，只要它們不會與此資訊清單中的其他任何連接埠號碼發生衝突，而且電腦/VM 上執行的其他任何應用程式還未使用該連接埠號碼即可 <br> **Placement Properties** - 其中說明這個節點類型的屬性，您之後將使用這些屬性做為系統服務或您服務的位置限制。這些屬性是使用者定義的索引鍵/值組，其可針對指定節點提供額外的中繼資料。節點屬性的範例包括節點是否有硬碟機或圖形卡、其硬碟機的磁針數、核心，以及其他實體屬性。<br> **Capacities** - 節點容量會定義特定節點可以使用的特定資源名稱和數量。例如，節點可能會定義它具有名為 "MemoryInMb" 的度量容量，而且預設有 2048 MB 的可用記憶體。這些容量會在執行階段使用，以確保需要特定資源數量的服務會放在可使用剩餘資源的節點上。<br>**IsPrimary** - 如果您已定義多個 NodeType，請確定只有一個使用值「true」設定為主要，這是系統服務的執行所在位置。其他所有節點類型應該設定為值「false」|
|**節點**|屬於叢集一部分的每個節點的詳細資料 (節點類型、節點名稱、IP 位址、節點的容錯網域和升級網域)。您想要建立叢集所在的電腦必須與其 IP 位址一起列在這裡。<br> 如果您為所有節點使用相同的 IP 位址，則會建立一整體叢集，您可以將此叢集用於測試之用。一整體叢集不得用於部署生產工作負載。|

### 步驟 2︰執行建立叢集指令碼
一旦您修改 JSON 文件中的叢集組態，並在其中加入所有節點資訊之後，執行封裝資料夾中的叢集建立「CreateServiceFabricCluster.ps1」PowerShell 指令碼，並傳入 JSON 組態檔的路徑及封裝 CAB 檔案的位置。

此指令碼可以在以系統管理員身分存取叢集組態檔中列為節點的所有電腦的任何電腦上執行。執行此指令碼所在的電腦不一定是叢集的一部分。

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] 您可以在用來執行 CreateServiceFabricCluster PowerShell 的 VM/電腦上本機使用部署記錄檔。您將可在您從中執行 Powershell 命令的資料夾中，名為「DeploymentTraces」的子資料夾中找到部署記錄檔。此外，若要查看 Service Fabric 是否已正確部署至電腦，您可以在 C:\\ProgramData 目錄中找到已安裝的檔案，並可在工作管理員中看到 FabricHost.exe 和 Fabric.exe 處理程序正在執行。

### 步驟 3：連接到叢集
現在，您可以使用 http://localhost:19080/Explorer/index.html 直接從其中一部電腦或是使用 http://<IPAddressofaMachine>:19080/Explorer/index.html 從遠端利用 Service Fabric Explorer 連接到叢集

## 將節點新增至叢集

1. 準備您要新增至叢集的 VM/電腦 (請參閱上述＜規劃及準備叢集部署＞小節中的步驟 2)。
2. 規劃為您即將新增此 VM/電腦的容錯網域和升級網域。
3. 複製或[下載適用於 Windows Server 的 Service Fabric 獨立套件](http://go.microsoft.com/fwlink/?LinkId=730690)，並將封裝解壓縮至您打算新增到叢集的 VM/電腦。
4. 開啟 PowerShell 系統管理員命令提示字元，瀏覽至解壓縮封裝的位置。
5. 使用描述要新增之新節點的參數執行「AddNode.ps1」Powershell。下列範例會將稱為 VM5 的新節點 (類型 NodeType0、IP 位址 182.17.34.52) 新增至 UD1 和 FD1。「ExistingClusterConnectionEndPoint」是已在現有叢集中之節點的 Connect Endpoint。您可以在叢集中對此選擇「任何」節點 IP 位址。

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## 從叢集移除節點

1. 以遠端桌面 (RDP) 登入到您想要從叢集移除的 VM/電腦。
2. 複製或[下載適用於 Windows Server 的 Service Fabric 獨立套件](http://go.microsoft.com/fwlink/?LinkId=730690)，並將封裝解壓縮至您打算新增到叢集的 VM/電腦。
3. 開啟 PowerShell 系統管理員命令提示字元，然後瀏覽至解壓縮封裝的位置。
4. 執行「RemoveNode.ps1」Powershell。下列範例會從叢集移除目前的節點。「ExistingClusterConnectionEndPoint」是已在現有叢集中之節點的 Connect Endpoint。您可以在叢集中對此選擇「任何」節點 IP 位址。

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## 移除叢集
若要移除叢集，請從封裝資料夾執行「RemoveServiceFabricCluster.ps1」Powershell 指令碼，然後傳入 JSON 組態檔的路徑及封裝 CAB 檔案的位置。

此指令碼可以在以系統管理員身分存取叢集組態檔中列為節點的所有電腦的任何電腦上執行。執行此指令碼所在的電腦不一定是叢集的一部分

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## 作法：使用 Azure IaaS VM 建立三個節點叢集
下列步驟說明如何使用獨立 Windows Server 安裝程式在 Azure IaaS VM 上建立叢集。請注意，與透過 Azure 入口網站所建立並由 Service Fabric 資源提供者所升級的叢集不同，此 IaaS 叢集上的 Service Fabric 執行階段完全由您管理。

1. 登入 Azure 入口網站，並在資源群組中建立新的 Windows Server 2012 R2 Datacenter VM。
2. 再新增兩個 Windows Server 2012 R2 Datacenter VM 至相同的資源群組。確定每個 VM 在建立時具有相同的系統管理員使用者名稱和密碼。一旦建立好，您應該會在相同的虛擬網路中看到這三個 VM。
3. 使用 [伺服器管理員] > [本機伺服器] 儀表板連接到每個 VM 並關閉 Windows 防火牆。這可確保網路流量可在電腦之間進行通訊。同時在每個電腦上，透過開啟命令提示字元並輸入「ipconfig」來取得 IP 位址。或者，您可以在 Azure 入口網站中選取資源群組的虛擬網路資源，以查看每部電腦的 IP 位址
4. 連接到其中一部電腦，並測試您是否可以成功 ping 到其他兩部電腦。
5. 連接到其中一個 VM，並將獨立 Windows Server 封裝下載到電腦上的新資料夾，然後解壓縮封裝。
6. 在記事本開啟「ClusterConfig.Unsecure.MultiMachine.json」檔案、使用電腦的三個 IP 位址編輯每個節點、在頂端變更叢集名稱，然後儲存檔案。下面顯示叢集資訊清單的部分範例，其中會顯示每部電腦的 IP 位址。

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

7. 開啟 Powershell ISE 視窗，瀏覽至您下載和解壓縮獨立安裝程式封裝並儲存上述資訊清單檔案的資料夾。執行下列 PowerShell 命令。

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. 您應該會看到 Powershell 執行、連接到每部電腦並建立叢集。大約 1 分鐘過後，您就可以透過在其中一個電腦 IP 位址 (例如 http://10.7.0.5:19080/Explorer/index.html) 連接到 Service Fabric Explorer，測試叢集是否已運作。由於這是 IaaS VM 上的獨立叢集，如果您想要保護其安全，就必須將憑證部署至 VM，或將其中一部電腦設定為用來進行 Windows 驗證的 Windows Server Active Directory (AD) 控制站，就像您在內部部署所做的一樣。若要設定安全的叢集，請參閱＜後續步驟＞。

## 後續步驟
- [在 Windows Server 或 Linux 上建立獨立的 Service Fabric 叢集](service-fabric-deploy-anywhere.md)
- [獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)

- [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)
- [使用 X509 憑證保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->