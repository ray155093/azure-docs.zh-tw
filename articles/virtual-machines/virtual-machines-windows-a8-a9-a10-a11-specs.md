<properties
 pageTitle="關於與 Windows 搭配使用的計算密集型 VM | Microsoft Azure"
 description="取得針對 Windows VM 和雲端服務使用 Azure H 系列及 A8、A9、A10 和 A11 計算密集型大小的背景資訊和考量"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# 關於 H 系列和計算密集型 A 系列 VM

這裡提供使用較新的 Azure H 系列和較舊的 A8、A9、A10 及 A11 執行個體 (也稱為「計算密集型」執行個體) 的背景資訊和一些考量。本文著重於這些 Windows VM 執行個體的使用。本文也適用於 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md)。


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

您可以建立支援 RDMA 的 Windows Server 執行個體叢集，並部署其中一個支援的 MPI 實作，以利用 Azure RDMA 網路。這個低延遲、高輸送量的網路只保留給 MPI 流量使用。

* **作業系統**
    * **虛擬機器** - Windows Server 2012 R2、Windows Server 2012
    * **雲端服務** - Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 客體 OS 系列

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 或更新版本、Intel MPI Library 5.x

支援的 MPI 實作使用 Microsoft Network Direct 介面在執行個體之間進行通訊。如需了解部署選項和範例組態步驟，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)和[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。


>[AZURE.NOTE]在支援 RDMA 的計算密集型 VM 上，必須將 HpcVmDrivers 擴充功能新增到 VM 中，才能安裝 RDMA 連線所需的 Windows 網路裝置驅動程式。在大多數部署中，會自動新增 HpcVmDrivers 擴充功能。如果您需要自行新增擴充功能，請參閱[管理 VM 擴充功能](virtual-machines-windows-classic-manage-extensions.md)。

## HPC Pack 和 Windows 的考量

您不需要 [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft 的免費 HPC 叢集和作業管理解決方案)，即可搭配 Windows Server 使用計算密集型執行個體。不過，它可為您提供一個選項，讓您能夠在 Azure 中建立計算叢集來執行 Windows 型 MPI 應用程式及其他 HPC 工作負載。HPC Pack 2012 R2 和更新版本包含 MS-MPI 的執行階段環境，此 MS-MPI 如果部署在支援 RDMA 的 VM 上，即可使用 Azure RDMA 網路。

如需在 Windows 伺服器上使用大量運算執行個體和 HPC Pack 的詳細資訊和檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。




## 後續步驟

* 如需有關計算密集型大小的可用性和價格詳細資料，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)和[雲端服務定價](https://azure.microsoft.com/pricing/details/cloud-services/)。

* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始使用 HPC Pack 在 Windows 上部署和使用計算密集型執行個體，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。

* 如需以 Azure Batch 使用 A8 和 A9 執行個體執行 MPI 應用程式的資訊，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

<!---HONumber=AcomDC_0928_2016-->