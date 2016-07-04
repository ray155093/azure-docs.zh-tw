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
   ms.date="06/21/2016"
   ms.author="chackdan"/>


# 在內部部署或雲端建立 Azure Service Fabric 叢集

Azure Service Fabric 可允許在執行 Windows Server 的任何虛擬機器或電腦上建立 Service Fabric 叢集。這表示您能夠在任何環境中，不論是在內部部署環境或是透過任何雲端提供者，只要有一組互連式 Windows Server 電腦，都可部署和執行 Service Fabric 應用程式。Service Fabric 會提供一個安裝套件供您建立這類的 Service Fabric 叢集。

本文將逐步引導您使用 Service Fabric 內部部署 (但可針對其他任何環境輕鬆地調整，例如其他雲端) 的獨立套件完成建立叢集的步驟。

>[AZURE.NOTE] 此獨立供應項目目前為預覽狀態。如果您想要立即下載一份 EULA，[按一下這裡](http://go.microsoft.com/fwlink/?LinkID=733084)。

<a id="downloadpackage"></a>
## 下載 Service Fabric 獨立封裝


[下載適用於 Windows Server 2012 R2 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)，名稱為「Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip」。

在下載套件中，您將會找到下列檔案︰

|**檔案名稱**|**簡短說明**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|CAB 檔，其中包含將部署到叢集中每部電腦的二進位檔。|
|ClusterConfig.Unsecure.DevCluster.JSON|叢集組態檔範例，其中包含適用於不安全、三個節點、單一 VM/電腦部署叢集的所有設定，包括叢集所屬每個節點的相關資訊。 |
|ClusterConfig.Unsecure.MultiMachine.JSON|叢集組態檔範例，其中包含適用於叢集的所有設定，包括不安全的多個 VM/電腦叢集所屬每部電腦的相關資訊。|
|ClusterConfig.Windows.DevCluster.JSON|叢集組態檔範例，其中包含適用於安全、三個節點、單一 VM/電腦部署叢集的所有設定，包括叢集所屬每個節點的相關資訊。使用 [Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護叢集的安全。|
|ClusterConfig.Windows.MultiMachine.JSON|叢集組態檔範例，其中包含適用於安全叢集的所有設定，包括安全的多個 VM/電腦叢集所屬每部電腦的相關資訊。使用 [Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護叢集的安全。|
|ClusterConfig.x509.DevCluster.JSON|叢集組態檔範例，其中包含適用於安全、三個節點、單一 VM/電腦部署叢集的所有設定，包括叢集所屬每個節點的相關資訊。使用 Windows x509 憑證保護叢集的安全。|
|ClusterConfig.x509.MultiMachine.JSON|叢集組態檔範例，其中包含適用於安全叢集的所有設定，包括安全的多個 VM/電腦叢集所屬每部電腦的相關資訊。使用 x509 憑證保護叢集的安全。|
|EULA.txt|使用 Microsoft Azure Service Fabric Service Fabric 獨立套件的授權條款。如果您想要立即下載一份 EULA，[按一下這裡](http://go.microsoft.com/fwlink/?LinkID=733084)。|
|Readme.txt|指向版本資訊和基本安裝指示的連結。這是您將會在此頁面上找到的一小部分指示。|
|CreateServiceFabricCluster.ps1|使用 ClusterConfig.JSON 檔案中的設定建立叢集的 PowerShell 指令碼。|
|RemoveServiceFabricCluster.ps1|使用 ClusterConfig.JSON 中的設定移除叢集的 PowerShell 指令碼。|
|AddNode.ps1|適用於叢集的 PowerShell 指令碼，可將節點加入至現有的叢集|
|RemoveNode.ps1|適用於叢集的 PowerShell 指令碼，可從叢集中移除節點|


## 規劃及準備叢集部署
建立叢集之前，必須先執行下列步驟。

### 步驟 1︰規劃叢集基礎結構
您即將在您所擁有的電腦上建立 Service Fabric 叢集，因此您可以決定您希望叢集不受何種失敗的影響。例如，您是否需要提供這些電腦的個別電源線或網際網路連線？ 此外，您也應該考慮這些電腦的實體安全性。實體位置在哪裡？誰需要存取這些電腦？ 一旦您做出這些決定之後，您要以邏輯的方式，將電腦對應到多個容錯網域 (如需詳細資訊，請參閱下方)。相較於測試叢集，生產叢集的基礎結構規劃更為複雜。

### 步驟 2︰準備符合必要條件的電腦
您想要新增到叢集的每部電腦的必要條件︰

- 建議至少有 2 GB 的記憶體
- 網路連線 – 請確定電腦位於一或多個安全的網路
- Windows Server 2012 R2 或 Windows Server 2012 (您必須為此系統安裝 KB2858668)。
- .NET Framework 4.5.1 或更高版本，完整安裝
- Windows PowerShell 3.0
- 部署和設定叢集的叢集系統管理員必須擁有每部電腦的系統管理員權限。
- RemoteRegistry 服務應該在所有電腦上執行。

### 步驟 3︰決定初始叢集大小
每個節點都包含完整的 Service Fabric 堆疊，而且是 Service Fabric 叢集的個別成員。在一般 Service Fabric 部署中，每個作業系統執行個體 (實體或虛擬) 都有一個節點。叢集大小取決於您的業務需求。不過，您必須至少有三個節點 (電腦/VM) 的叢集大小。請注意，基於開發目的，在一部指定的電腦上可以有多個節點。在生產環境中，對於每個實體或虛擬機器，Service Fabric 只支援一個節點。

### 步驟 4︰決定容錯網域和升級網域的數目
**容錯網域 (FD)** 是故障的實體單元，而且與資料中心內的實體基礎結構直接相關。容錯網域是由共用單一失敗點的硬體元件 (電腦、交換器等等) 所組成。雖然容錯網域和機架之間沒有 1:1 對應，但是大致上來說，可以將每個機架視為一個容錯網域。考慮叢集中的節點時，強烈建議至少在三個容錯網域之間散佈節點。

當您在 *ClusterConfig.JSON* 中指定 FD 時，可以選擇 FD 的名稱。Service Fabric 支援階層式 FD，因此，您可以在 FD 中反映您的基礎結構拓撲。例如，允許下列內容：

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**升級網域 (UD)** 是節點的邏輯單元。在 Service Fabric 協調式升級 (應用程式升級或叢集升級) 期間，會關閉 UD 中的所有節點來執行升級，而其他 UD 中的節點則仍然可用來提供要求。您對電腦所進行的韌體升級將不會接受 UD，因此您必須一次在一部電腦上進行。

思考這些概念最簡單的方式就是將 FD 視為非計劃性故障的單元，並將 UD 視為計劃性維護的單元。

當您在 *ClusterConfig.JSON* 中指定 UD 時，可以選擇 UD 的名稱。例如，允許下列所有內容：

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

### 步驟 5︰下載適用於 Windows Server 的 Service Fabric 獨立封裝
[下載適用於 Windows Server 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)，並將封裝解壓縮至不屬於叢集一部分的部署電腦，或解壓縮至將所屬於叢集的其中一部電腦。

<a id="createcluster"></a>
## 建立叢集

完成以上規劃和準備一節中所述的步驟之後，您現在就可以開始建立您的叢集。

### 步驟 1︰修改叢集組態
從您下載的封裝開啟 *ClusterConfig.JSON*。您可以使用您選擇的任何編輯器，並修改下列設定︰

|**組態設定**|**說明**|
|-----------------------|--------------------------|
|NodeTypes|節點類型可讓您將叢集節點分成不同的群組。一個叢集至少必須有一個節點類型。群組中的所有節點都有下列共同的特性。<br> *Name* - 這是節點類型名稱。<br> *EndPoints* - 這些是與這個節點類型相關聯的各種具名端點 (連接埠)。您可以使用任何您希望的連接埠號碼，只要它們不會與此資訊清單中的其他任何連接埠號碼發生衝突，而且電腦/VM 上的其他任何程式還未使用該連接埠號碼即可 <br> *PlacementProperties* - 其中說明這個節點類型的屬性，您之後將使用這些屬性做為系統服務或您服務的位置限制。這些屬性是使用者定義的索引鍵/值組，其可針對指定節點提供額外的中繼資料。節點屬性的範例包括節點是否有硬碟機或圖形卡、其硬碟機的磁針數、核心，以及其他實體屬性。<br> *Capacities* - 節點容量會定義特定節點可以使用的特定資源名稱和數量。例如，節點可能會定義它具有名為 "MemoryInMb" 的度量容量，而且預設有 2048 MB 的可用記憶體。這些容量會在執行階段使用，以確保需要特定資源數量的服務會放在可使用剩餘資源的節點上。|
|節點|將屬於叢集一部分的每個節點的詳細資料 (節點類型、節點名稱、IP 位址、節點的容錯網域和升級網域)。您想要建立叢集所在的電腦必須與其 IP 位址一起列在這裡。<br>如果您為所有節點使用相同的 IP 位址，則會建立一整體叢集，您可以將此叢集用於測試之用。一整體叢集不得用於部署生產工作負載。|

### 步驟 2︰執行建立叢集指令碼
一旦您修改 JSON 文件中的叢集組態，並在其中加入所有節點資訊之後，執行封裝資料夾中的叢集建立 PowerShell 指令碼，並傳入組態檔的路徑及封裝根目錄的位置。

此指令碼可以在以系統管理員身分存取叢集組態檔中列為節點的所有電腦的任何電腦上執行。執行此指令碼所在的電腦不一定是叢集的一部分。

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.JSON -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

>[AZURE.NOTE] 您可以在執行 CreateServiceFabricCluster PowerShell 的 VM/電腦上本機使用部署記錄檔。您將可在您從中執行 PS 命令的資料夾中，名為「DeploymentTraces」的子資料夾中找到部署記錄檔。

## 將節點新增至您的 Service Fabric 叢集 

1. 準備您要新增至叢集的 VM/電腦 (請參閱上述＜規劃及準備叢集部署＞小節中的步驟 2)。 
2. 規劃為您即將新增此 VM/電腦的容錯網域和升級網域。
3. [下載適用於 Windows Server 的 Service Fabric 獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)，並將封裝解壓縮至您打算新增到叢集的 VM/電腦。 
4. 開啟 PowerShell 系統管理員命令提示字元，瀏覽至解壓縮封裝的位置
5. 執行 AddNode.PS1

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.52:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## 移除 Service Fabric 叢集的節點 

1. TS 到您想要從叢集移除的 VM/電腦
2. 開啟 PowerShell 系統管理員命令提示字元，瀏覽至解壓縮封裝的位置
5. 執行 RemoveNode.PS1

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.52:19000
```

## 刪除您的 Service Fabric 叢集 
1. TS 至叢集所屬的其中一個 VM/電腦
2. 開啟 PowerShell 系統管理員命令提示字元，瀏覽至解壓縮封裝的位置
5. 執行 RemoveNode.PS1

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.52:19000
```


## 後續步驟
- [了解叢集安全性](service-fabric-cluster-security.md)
- [ Service Fabric SDK 和開始使用](service-fabric-get-started.md)
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [獨立叢集建立功能以及與 Azure 受管理叢集比較的概觀](service-fabric-deploy-anywhere.md)

<!---HONumber=AcomDC_0622_2016-->