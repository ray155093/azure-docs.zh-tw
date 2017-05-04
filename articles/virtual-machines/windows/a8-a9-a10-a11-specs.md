---
title: "關於與 Windows 搭配使用的計算密集型 VM | Microsoft Docs"
description: "取得針對 Windows VM 和雲端服務使用 Azure H 系列及 A8、A9、A10 和 A11 計算密集型大小的背景資訊和考量"
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7f86fb57d9082e19b506c42737aaa020d474d599
ms.lasthandoff: 04/27/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>有關適用於 Windows 的 H 系列和計算密集型 A 系列 VM
這裡提供使用較新的 Azure H 系列和較舊的 A8、A9、A10 及 A11 執行個體 (也稱為「計算密集型」  執行個體) 的背景資訊和一些考量。 本文著重於這些 Windows VM 執行個體的使用。 您也可以針對 [Linux VM](../linux/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 使用它們。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱[虛擬機器的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>存取 RDMA 網路
若要存取 Azure RDMA 網路的 Windows MPI 流量，具備 RDMA 功能的執行個體必須符合下列需求︰ 

* **作業系統**
  
  * **虛擬機器** - Windows Server 2012 R2、Windows Server 2012
  * **雲端服務** - Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 客體 OS 系列

    > [!NOTE]
    > Windows Server 2016 目前不支援 Azure 中的 RDMA 連線能力。
    >
    
* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 或更新版本、Intel MPI Library 5.x

  支援的 MPI 實作使用 Microsoft Network Direct 介面在執行個體之間進行通訊。 
* **HpcVmDrivers VM 擴充** - 在具備 RDMA 功能的 VM 上，HpcVmDrivers 擴充必須新增以安裝 Windows 網路裝置驅動程式，該驅動程式會啟用 RDMA 連接。 (在某些 A8 和 A9 執行個體部署中，會自動新增 HpcVmDrivers 擴充功能)。如果您需要將 VM 擴充功能新增至 VM，您可以使用 [Azure PowerShell](/powershell/azure/overview) Cmdlet。 

  
  例如，若要在已部署於 Resource Manager 部署模型中且名為 myVM 之現有具備 RDMA 功能的 VM 上安裝最新版本 1.1 HpcVMDrivers 擴充功能：

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  如需詳細資訊，請參閱[虛擬機器擴充功能和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 您也可以針對已在[傳統部署模型](classic/manage-extensions.md)中部署的 VM 使用擴充功能。


## <a name="considerations-for-hpc-pack-and-windows"></a>HPC Pack 和 Windows 的考量
您不需要 [Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft 的免費 HPC 叢集和作業管理解決方案)，即可搭配 Windows Server 使用計算密集型執行個體。 不過，它可為您提供一個選項，讓您能夠在 Azure 中建立計算叢集來執行 Windows 型 MPI 應用程式及其他 HPC 工作負載。 HPC Pack 2012 R2 和更新版本包含 MS-MPI 的執行階段環境，此 MS-MPI 如果部署在支援 RDMA 的 VM 上，即可使用 Azure RDMA 網路。

如需在 Windows 伺服器上使用大量計算執行個體和 HPC Pack 的詳細資訊和檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="next-steps"></a>後續步驟
* 如需有關計算密集型大小的可用性和價格詳細資料，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)和[雲端服務定價](https://azure.microsoft.com/pricing/details/cloud-services/)。
* 如需儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要開始使用 HPC Pack 在 Windows 上部署和使用計算密集型執行個體，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* 如需以 Azure Batch 使用	計算密集型執行個體執行 MPI 應用程式的資訊，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../../batch/batch-mpi.md)。


