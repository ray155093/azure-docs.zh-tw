<properties
 pageTitle="關於 A8、A9、A10、A11 VM 大小和 Windows | Microsoft Azure"
 description="取得使用 Windows VM 和雲端服務適用的 Azure A8、A9、A10 和 A11 密集計算大小的背景資訊和考量"
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
 ms.date="08/04/2016"
 ms.author="danlep"/>

# 關於 A8、A9、A10 和 A11 密集運算執行個體

這裡提供使用 Azure A8、A9、A10 和 A11 執行個體 (也稱為「計算密集」執行個體) 的背景資訊和一些考量。本文著重於這些 Windows VM 執行個體的使用。本文也適用於 [Linux VM](virtual-machines-linux-a8-a9-a10-a11-specs.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

在單一雲端服務、可用性設定組或 Azure Batch 集區中的 A8 和 A9 執行個體可以存取 Azure 中的 RDMA 網路，來執行會使用 Microsoft Network Direct 介面在執行個體之間通訊的 Windows MPI 應用程式。

請遵循 MPI 應用程式的必要條件，以存取 Windows 虛擬機器中的 RDMA 網路、雲端服務、A8 或 A9 執行個體的 Azure Batch 集區。如需典型部署案例，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)和[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。


必要條件 | 虛擬機器 | 雲端服務或 Batch 集區 
---------- | ------------ | ------------- 
作業系統 | Windows Server 2012 R2 或 Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 客體 OS 系統系列 
MPI | MS-MPI 2012 R2 或更新版本，或 Intel MPI Library 5 | MS-MPI 2012 R2 或更新版本，或 Intel MPI Library 5 


>[AZURE.NOTE]在 A8 和 A9 大小的虛擬機器上，必須將 HpcVmDrivers 擴充功能加入 VM 中，才能安裝 RDMA 連線所需的 Windows 網路裝置驅動程式。取決於您的部署方法，HpcVmDrivers 擴充功能可能會自動加入到 A8 或 A9 VM 中，或者您可能需要自行予以加入。若要自行加入擴充功能，請參閱[管理 VM 擴充功能](virtual-machines-windows-classic-manage-extensions.md)。

## HPC Pack 和 Windows 的考量

將 A8、A9、A10 和 A11 執行個體與 Windows Server 搭配使用時，並不需要 HPC Pack，但它是在 Azure 中執行會存取 RDMA 網路之 Windows MPI 應用程式的實用工具。HPC Pack 2012 R2 和更新版本包含 Windows 訊息傳遞介面 (MS-MPI) 之 Microsoft 實作的執行階段環境，當部署在 A8 或 A9 執行個體上時可使用 Azure RDMA 網路。

如需在 Windows 伺服器上使用大量運算執行個體和 HPC Pack 的詳細資訊和檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。




## 後續步驟

* 如需 A8、A9、A10 和 A11 執行個體的可用性和價格詳細資訊，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)和[雲端服務定價](https://azure.microsoft.com/pricing/details/cloud-services/)。

* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始在 Windows 上部署和使用 A8 和 A9 執行個體及 HPC Pack，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。

* 如需以 Azure Batch 使用 A8 和 A9 執行個體執行 MPI 應用程式的資訊，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

<!---HONumber=AcomDC_0810_2016-->