<properties
 pageTitle="關於 A8、A9、A10、A11 VM 大小和 Linux | Microsoft Azure"
 description="取得針對 Linux VM 使用 Azure A8、A9、A10 和 A11 計算密集大小的背景資訊和考量。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# 關於 A8、A9、A10 和 A11 密集運算執行個體 

這裡提供使用 Azure A8、A9、A10 和 A11 執行個體 (也稱為「計算密集」執行個體) 的背景資訊和一些考量。本文著重於這些 Linux VM 執行個體的使用。本文也適用於 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

在單一雲端服務或可用性設定組中，A8 和 A9 大小 Linux VM 的叢集 (執行下列其中一個支援的 Linux HPC 發行版本，以及支援的 MPI 實作) 可以在 Azure 中存取 RDMA 網路，以執行 Linux MPI 應用程式。如需部署選項和範例組態步驟，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

* **發行版本** - SUSE Linux Enterprise Server (SLES) 12 for HPC、SLES 12 for HPC (Premium)、CentOS 型 7.1 HPC 或 CentOS 型 6.5 HPC，從 Azure Marketplace 映像部署

* **MPI** - Intel MPI Library 5.x

    >[AZURE.NOTE] 已在 Marketplace 中的 CentOS 型 HPC 映像上安裝 Intel MPI 5.1。若要使用 SLES 12 HPC VM 上的 Intel MPI，您必須個別安裝它。

目前，Azure Linux RDMA 驅動程式只會在您從 Azure Marketplace 部署啟用 RDMA 的 SLES 12 HPC 和 CentOS HPC 映像時安裝。您無法在您部署的其他 Linux VM 上安裝驅動程式。

>[AZURE.NOTE]在來自 Marketplace 的 CentOS 型 HPC 映像上，核心更新已在 **yum** 組態檔中停用。這是因為 Linux RDMA 驅動程式以 RPM 封裝散發，如果更新核心，驅動程式更新可能無法運作。


## SLES 12 的 RDMA 驅動程式更新
在您建立以 SLES 12 HPC 映像為基礎的 VM 之後，您必須針對 RDMA 網路連線更新 VM 上的 RDMA 驅動程式。

>[AZURE.IMPORTANT]所有 Azure 區域中的 SLES 12 HPC VM 部署都**需要**這個步驟。如果您已部署 CentOS 型 7.1 HPC 或 6.5 HPC VM，就不需要這個步驟。

更新驅動程式之前，請先停止 VM 上所有的 **zypper** 處理程序或任何鎖定 SUSE 儲存機制資料庫的處理程序。否則驅動程式可能無法正確更新。

若要更新每部 VM 上的 Linux RDMA 驅動程式，請從您的用戶端電腦執行下列其中一組 Azure CLI 命令。

**針對在傳統部署模型中佈建的 SLES 12 HPC VM**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**針對在 Resource Manager 部署模型中佈建的 SLES 12 HPC VM**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]可能需要一些時間來安裝驅動程式，且命令會在沒有輸出的情況下傳回。更新之後，您的 VM 將會重新啟動，並應該在幾分鐘內準備好以供使用。

### 驅動程式更新的範例指令碼

如果您具有 SLES 12 HPC VM 的叢集，您可以針對跨叢集中所有節點的驅動程式更新編寫指令碼。例如，下列指令碼會更新 8 節點叢集內的驅動程式。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## HPC Pack 和 Linux 的考量

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 的免費 HPC 叢集和作業管理解決方案。HPC Pack 2012 R2 的最新版本支援讓數個 Linux 發行版在部署於 Azure VM 中，且由 Windows Server 前端節點管理的計算節點上執行。部署在 A8 或 A9 VM 上並執行支援 MPI 實作的 Linux 計算節點可以執行存取 RDMA 網路的 MPI 應用程式。如需詳細資訊，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](virtual-machines-linux-classic-hpcpack-cluster.md)。

## 網路拓撲考量

* 在 Azure 中的 A8 或 A9 大小 Linux VM 上，Eth1 會保留給 RDMA 網路流量。請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。Eth0 會保留給一般 Azure 網路流量。

* 在 Azure 中，不支援透過 Infiniband (IB) 的 IP。僅支援透過 IB 的 RDMA。


## 後續步驟

* 如需 A8、A9、A10 和 A11 執行個體的可用性和價格詳細資訊，請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。

* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始在 Linux 上部署和使用 A8 和 A9 執行個體及 RDMA，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

<!---HONumber=AcomDC_0810_2016-->