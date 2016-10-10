<properties
 pageTitle="關於與 Linux 搭配使用的計算密集型 VM | Microsoft Azure"
 description="取得針對 Linux VM 使用 H 系列及 A8、A9、A10 和 A11 計算密集型大小的背景資訊和考量"
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
 ms.date="09/21/2016"
 ms.author="danlep"/>

# 關於 H 系列和計算密集型 A 系列 VM 

這裡提供使用較新的 Azure H 系列和較舊的 A8、A9、A10 及 A11 大小 (也稱為「計算密集型」執行個體) 的背景資訊和一些考量。本文將焦點放在使用這些 Linux VM 大小。本文也適用於 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md)。




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

您可以建立支援 RDMA 的 Linux VM 叢集來執行下列其中一個支援的 Linux HPC 散發套件及支援的 MPI 實作，以利用 Azure RDMA 網路。如需部署選項和範例組態步驟，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

* **散發套件** - 您必須從 Azure Marketplace 中支援 RDMA 的 SUSE Linux Enterprise Server (SLES) 或 OpenLogic CentOS 型 HPC 映像部署 VM。只有下列 Marketplace 映像支援必要的 Linux RDMA 驅動程式︰

    * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)
    
    * SLES 12 for HPC、SLES 12 for HPC (Premium)
    
    * CentOS 型 7.1 HPC
    
    * CentOS 型 6.5 HPC
    
    >[AZURE.NOTE]針對 H 系列 VM，建議使用 SLES 12 SP1 for HPC 映像或 CentOS 型 7.1 HPC。
    >
    >在 CentOS 型 HPC 映像上，**yum** 組態檔中已停用核心更新。這是因為 Linux RDMA 驅動程式以 RPM 封裝散發，如果更新核心，驅動程式更新可能無法運作。

* **MPI** - Intel MPI Library 5.x

    視您選擇的 Marketplace 映像而定，可能需要個別的 Intel MPI 授權、安裝或組態，如下︰
    
    * **SLES 12 SP1 for HPC 映像** - 請執行下列命令來安裝散佈在 VM 上的 Intel MPI 套件：
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 for HPC 映像** - 您必須個別註冊，才能下載並安裝 Intel MPI。請參閱 [Intel MPI Library 安裝指南](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf)。
    
    * **CentOS 型 HPC 映像** - 已經安裝 Intel MPI 5.1。

    必須進行額外的系統設定，才能在叢集 VM 上執行 MPI 作業。例如，在 VM 叢集上，您必須在計算節點之間建立信任關係。如需了解一般設定，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。


## HPC Pack 和 Linux 的考量

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 的免費 HPC 叢集和作業管理解決方案，提供您一個搭配 Linux 使用計算密集型執行個體的選項。HPC Pack 2012 R2 的最新版本支援讓數個 Linux 散發套件在部署於 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。搭配支援 RDMA 且執行 Intel MPI 的 Linux 計算節點時，HPC Pack 可以排定及執行存取 RDMA 網路的 Linux MPI 應用程式。如需詳細資訊，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](virtual-machines-linux-classic-hpcpack-cluster.md)。

## 網路拓撲考量

* 在 Azure 中具備 RDMA 功能的 Linux VM 上，Eth1 會保留給 RDMA 網路流量使用。請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。Eth0 會保留給一般 Azure 網路流量。

* 在 Azure 中，不支援透過 InfiniBand (IB) 的 IP。僅支援透過 IB 的 RDMA。

## SLES 12 的 RDMA 驅動程式更新

在根據 SLES 12 HPC 映像建立 VM 之後，您可能需要更新 VM 上的 RDMA 驅動程式，才能進行 RDMA 網路連線。

>[AZURE.IMPORTANT]所有 Azure 區域中的 SLES 12 for HPC VM 部署都「需要」進行這個步驟。如果您部署的是 SLES 12 SP1 for HPC、CentOS 型 7.1 HPC 或 CentOS 型 6.5 HPC VM，則不需要進行這個步驟。

更新驅動程式之前，請先停止 VM 上所有的 **zypper** 處理程序或任何鎖定 SUSE 儲存機制資料庫的處理程序。否則驅動程式可能無法正確更新。

若要更新每部 VM 上的 Linux RDMA 驅動程式，請從您的用戶端電腦執行下列其中一組 Azure CLI 命令。

**在傳統部署模型中佈建的 SLES 12 for HPC VM**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**在 Resource Manager 部署模型中佈建的 SLES 12 for HPC VM**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]安裝驅動程式可能需要花一些時間，而且命令會在沒有輸出的情況下傳回。更新之後，您的 VM 將會重新啟動，並應該在幾分鐘內準備好以供使用。

### 驅動程式更新的範例指令碼

如果您有 SLES 12 for HPC VM 的叢集，您可以編寫跨叢集中所有節點進行驅動程式更新的指令碼。例如，下列指令碼會更新 8 節點叢集內的驅動程式。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## 後續步驟

* 如需有關計算密集型大小的可用性和價格的詳細資料，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。

* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始在 Linux 上部署及使用具有 RDMA 的計算密集型大小，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

<!---HONumber=AcomDC_0928_2016-->