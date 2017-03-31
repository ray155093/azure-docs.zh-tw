---
title: "關於與 Linux 搭配使用的計算密集型 VM | Microsoft Docs"
description: "取得針對 Linux VM 使用 H 系列及 A8、A9、A10 和 A11 計算密集型大小的背景資訊和考量"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e2e39b0fa9c45027c51bc5e063df990faf934ff5
ms.lasthandoff: 03/27/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>關於 Linux 的 H 系列和計算密集型 A 系列 VM
這裡提供使用較新的 Azure H 系列和較舊的 A8、A9、A10 及 A11 大小 (也稱為「計算密集型」  執行個體) 的背景資訊和一些考量。 本文將焦點放在使用這些 Linux VM 大小。 您也可以針對 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 使用它們。 

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>存取 RDMA 網路
您可以建立支援 RDMA 的 Linux VM 叢集來執行下列其中一個支援的 Linux HPC 散發套件及支援的 MPI 實作，以利用 Azure RDMA 網路。 如需部署選項和範例組態步驟，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

* **散發套件** - 您必須從 Azure Marketplace 中支援 RDMA 的 SUSE Linux Enterprise Server (SLES) 或 OpenLogic CentOS 型 HPC 映像部署 VM。 下列 Marketplace 映像支援 RDMA 連線能力：
  
    * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)
    
    * CentOS 型 7.1 HPC、CentOS 型 6.5 HPC  
 
        > [!NOTE]
        > 針對 H 系列 VM，建議使用 SLES 12 SP1 for HPC 映像或 CentOS 型 7.1 HPC。
        >
        > 在 CentOS 型 HPC 映像上， **yum** 組態檔中已停用核心更新。 這是因為 Linux RDMA 驅動程式以 RPM 封裝散發，如果更新核心，驅動程式更新可能無法運作。
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    視您選擇的 Marketplace 映像而定，可能需要個別的 Intel MPI 授權、安裝或組態，如下︰ 
  
  * **SLES 12 SP1 for HPC 映像** - Intel MPI 封裝是在 VM 上散發。 執行下列命令進行安裝：

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **CentOS 型 HPC 映像** - 已經安裝 Intel MPI 5.1。  
    
    必須進行額外的系統設定，才能在叢集 VM 上執行 MPI 作業。 例如，在 VM 叢集上，您必須在計算節點之間建立信任關係。 如需了解一般設定，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="considerations-for-hpc-pack-and-linux"></a>HPC Pack 和 Linux 的考量
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)是 Microsoft 的免費 HPC 叢集和作業管理解決方案，提供您一個搭配 Linux 使用計算密集型執行個體的選項。 HPC Pack 的最新版本支援讓數個 Linux 散發套件在部署於 Azure VM 中、由 Windows Server 前端節點管理的計算節點上執行。 搭配支援 RDMA 且執行 Intel MPI 的 Linux 計算節點時，HPC Pack 可以排定及執行存取 RDMA 網路的 Linux MPI 應用程式。 如需詳細資訊，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](linux/classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="network-topology-considerations"></a>網路拓撲考量
* 在 Azure 中具備 RDMA 功能的 Linux VM 上，Eth1 會保留給 RDMA 網路流量使用。 請勿變更任何 Eth1 設定或參考到此網路之組態檔中的任何資訊。 Eth0 會保留給一般 Azure 網路流量。
* 在 Azure 中，不支援透過 InfiniBand (IB) 的 IP。 僅支援透過 IB 的 RDMA。



## <a name="next-steps"></a>後續步驟
* 如需有關計算密集型大小的可用性和價格的詳細資料，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。
* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要開始在 Linux 上部署及使用具有 RDMA 的計算密集型大小，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。


