<properties
   pageTitle="用來部署 Windows HPC 叢集的 PowerShell 指令碼 | Microsoft Azure"
   description="執行 PowerShell 指令碼，以在 Azure 基礎結構服務中部署 Windows HPC Pack 叢集"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# 使用 HPC Pack IaaS 部署指令碼建立 Windows 高效能運算 (HPC) 叢集

在用戶端電腦上執行 HPC Pack IaaS 部署 PowerShell 指令碼，以在 Azure 基礎結構服務 (IaaS) 中為 Windows 工作負載部署完整的 HPC 叢集。如果您想要在 Azure 中為 Linux 工作負載部署 HPC Pack 叢集，請參閱[使用 HPC Pack IaaS 部署指令碼建立 Linux HPC 叢集](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## 範例組態檔

### 範例 1

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中有 1 個具有本機資料庫的前端節點，和 12 個套用了 BGInfo VM 延伸模組的運算節點。對於網域樹系中的所有 VM，都會停用 Windows 更新的自動安裝。所有雲端服務都直接建立在「東亞」位置中。計算節點建立在 3 個雲端服務和 3 個儲存體帳戶中 (即 _MyHPCCNService01_ 和 _mycnstorage01_ 中的 _MyHPCCN-0001_ 至 _MyHPCCN-0005_；_MyHPCCNService02_ 和 _mycnstorage02_ 中的 _MyHPCCN-0006_ 至 _MyHPCCN0010_；以及 _MyHPCCNService03_ 和 _mycnstorage03_ 中的 _MyHPCCN-0011_ 至 _MyHPCCN-0012_)。運算節點會從擷取自雲端節點的現有私人映像建立。自動增加和縮減服務會根據預設的增加和縮減間隔來啟用。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### 範例 2

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中包含 1 個前端節點、1 個具有 500 GB 資料磁碟的資料庫伺服器、2 個執行 Windows Server 2012 R2 作業系統的訊息代理程式節點，和 5 個執行 Windows Server 2012 R2 作業系統的運算節點。雲端服務 MyHPCCNService 會建立在同質群組 *MyIBAffinityGroup* 中，其他所有的雲端服務則建立在同質群組 *MyAffinityGroup* 中。前端節點上會啟用 HPC 工作排程器 REST API 和 HPC Web 入口網站。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### 範例 3

下列組態檔會部署一個 HPC Pack 叢集，其中包含一個具有本機資料庫的前端節點，以及 5 個執行 Windows Server 2008 R2 作業系統的運算節點。所有雲端服務都直接建立在「東亞」位置中。前端節點會做為網域樹系的網域控制站。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### 範例 4

下列組態檔會在現有的網域樹系中部署 HPC Pack 叢集。叢集中包含 1 個具有本機資料庫的前端節點，並建立了兩個 Azure 節點範本，且針對 Azure 節點範本 _AzureTemplate1_ 建立了 3 個中型大小的 Azure 節點。指令碼檔案會在前端節點設定後執行於前端節點上。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## 疑難排解


* **「VNet 不存在」錯誤** - 如果您執行 HPC Pack IaaS 部署指令碼，在一個訂用帳戶下同時將多個叢集部署於 Azure 中，則可能會有一或多個部署失敗，並顯示錯誤「VNet *VNet\_Name* 不存在」。如果發生此錯誤，請對失敗的部署重新執行指令碼。

* **從 Azure 虛擬網路存取網際網路時發生問題** - 如果您使用部署指令碼建立具有新網域控制站的 HPC Pack 叢集，或手動將前端節點 VM 升級到網域控制站，則在將 Azure 虛擬網路中的 VM 連接到網際網路時，可能會發生問題。如果在網域控制站上自動設定轉寄站 DNS 伺服器，且這個轉寄站 DNS 伺服器未正確解析，就可能出現這種狀況。

    若要解決此問題，請登入網域控制站，並選擇移除轉寄站組態設定，或設定有效的轉寄站 DNS 伺服器。若要這樣做，請在伺服器管理員中按一下 [工具] > [DNS] 以開啟 DNS 管理員，然後按兩下 [轉寄站]。

* **從大小為 A8 或 A9 的 VM 存取 RDMA 網路時發生問題** - 如果您使用部署指令碼新增 Windows Server 運算節點或大小為 A8 或 A9 的訊息代理程式節點 VM，您將這些 VM 連接到 RDMA 應用程式網路時可能會發生問題。之所以會發生此狀況，其中的一個原因是在大小為 A8 或 A9 的 VM 新增至叢集未正確安裝 HpcVmDrivers 延伸模組。比方說，延伸模組可能卡在安裝中狀態。

    若要解決這個問題，請先檢查 VM 中的延伸模組狀態。如果延伸模組未正確安裝，請嘗試從 HPC 叢集中移除節點，然後重新新增節點。例如，您可以在前端節點上執行 Add-HpcIaaSNode.ps1 指令碼，以新增運算節點 VM。
    
## 後續步驟

* 嘗試在叢集上執行測試工作負載。如需範例，請參閱 HPC Pack [快速入門指南](https://technet.microsoft.com/library/jj884144)。

* 如需使用指令碼來建立叢集並執行 HPC 工作負載的教學課程，請參閱[開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載](virtual-machines-windows-excel-cluster-hpcpack.md)。

* 嘗試以 HPC Pack 的工具啟動、停止、新增和移除您所建立之叢集中的運算節點。請參閱[在 Azure 中管理 HPC Pack 叢集的計算節點](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)。

* 您也可以使用 Azure 資源管理員範本來部署 HPC Pack 叢集。如需範例，請參閱[Create an HPC cluster (建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) 和 [Create an HPC cluster with a custom compute node image (使用自訂的計算節點映像建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)。

<!---HONumber=AcomDC_0629_2016-->