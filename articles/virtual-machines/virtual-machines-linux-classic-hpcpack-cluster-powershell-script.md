<properties
   pageTitle="用來部署 Linux HPC 叢集的 PowerShell 指令碼 | Microsoft Azure"
   description="執行 PowerShell 指令碼，以在 Azure 基礎結構服務中部署 Linux HPC Pack 叢集"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# 使用 HPC Pack IaaS 部署指令碼建立 Linux 高效能運算 (HPC) 叢集

在用戶端電腦上執行 HPC Pack IaaS 部署 PowerShell 指令碼，以在 Azure 基礎結構服務 (IaaS) 中為 Linux 工作負載部署完整的 HPC 叢集。如果您想要在 Azure 中為 Windows 工作負載部署 HPC Pack 叢集，請參閱[使用 HPC Pack IaaS 部署指令碼建立 Windows HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## 範例組態檔

### 範例 1

下列組態檔會建立新的網域樹系並部署 HPC Pack 叢集，其中包含 1 個具有本機資料庫的前端節點和 20 個 Linux 計算節點。所有雲端服務都直接建立在「東亞」位置中。Linux 計算節點會建立在 4 個雲端服務和 4 個儲存體帳戶中 (即 _MyLnxCNService01_ 和 _mylnxstorage01_ 中的 _MyLnxCN-0001_ 至 _MyLnxCN-0005_、_MyLnxCNService02_ 和 _mylnxstorage02_ 中的 _MyLnxCN-0006_ 至 _MyLnxCN-0010_、_MyLnxCNService03_ 和 _mylnxstorage03_ 中的 _MyLnxCN-0011_ 至 _MyLnxCN-0015_，以及 _MyLnxCNService04_ 和 _mylnxstorage04_ 中的 _MyLnxCN-0016_ 至 _MyLnxCN-0020_)。運算節點會從 OpenLogic CentOS 7.0 版 Linux 映像建立。

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## 疑難排解

* **「VNet 不存在」錯誤** - 如果您執行 HPC Pack IaaS 部署指令碼，在一個訂用帳戶下同時將多個叢集部署於 Azure 中，則可能會有一或多個部署失敗，並顯示錯誤「VNet *VNet\_Name* 不存在」。如果發生此錯誤，請對失敗的部署重新執行指令碼。

* **從 Azure 虛擬網路存取網際網路時發生問題** - 如果您使用部署指令碼建立具有新網域控制站的 HPC Pack 叢集，或手動將前端節點 VM 升級到網域控制站，則在將 Azure 虛擬網路中的 VM 連接到網際網路時，可能會發生問題。如果在網域控制站上自動設定轉寄站 DNS 伺服器，且這個轉寄站 DNS 伺服器未正確解析，就可能出現這種狀況。

    若要解決此問題，請登入網域控制站，並選擇移除轉寄站組態設定，或設定有效的轉寄站 DNS 伺服器。若要這樣做，請在伺服器管理員中按一下 [工具] > [DNS] 以開啟 DNS 管理員，然後按兩下 [轉寄站]。
    
## 後續步驟

* 如需使用指令碼來建立叢集並執行 Linux HPC 工作負載的教學課程，請參閱[在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md) 或[在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)。

* 您也可以使用 Azure 資源管理員範本來部署 HPC Pack 叢集。如需範例，請參閱 [Create an HPC cluster with Linux compute nodes (使用 Linux 計算節點建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)。

<!---HONumber=AcomDC_0406_2016-->