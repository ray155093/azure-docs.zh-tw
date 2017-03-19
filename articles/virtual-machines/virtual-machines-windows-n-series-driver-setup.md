---
title: "適用於 Windows 的 Azure N 系列驅動程式設定 | Microsoft Docs"
description: "如何針對 Azure 中執行 Windows 的 N 系列 VM 設定 NVIDIA GPU 驅動程式"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 57d7475db8183cfaad017fc934210d0481868d5f
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-windows-vms"></a>為 N 系列 Windows VM 設定 GPU 驅動程式
若要利用 Azure N 系列 VM (執行 Windows Server) 的 GPU 功能，您必須在部署之後於每個 VM 上安裝 NVIDIA 圖形驅動程式。 本文也適用於 [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需基本規格、儲存體容量與磁碟的詳細資料，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




## <a name="supported-gpu-drivers"></a>支援的 GPU 驅動程式

由遠端桌面連接至每個 N 系列 VM。 下載、擷取及安裝 Windows 作業系統支援的驅動程式。 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>NV VM 的 NVIDIA GRID 驅動程式

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>NC VM 的 NVIDIA Tesla 驅動程式

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>確認驅動程式安裝

在 Azure NV VM 上，安裝驅動程式之後必須重新啟動。 在 NC VM 上，則不需要重新啟動。

您可以在 [裝置管理員] 中確認驅動程式安裝。 下列範例會顯示 Azure NC VM 上成功的 K80 卡組態。

![GPU 驅動程式屬性](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

若要查詢 GPU 裝置狀態，請執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface)命令列公用程式。 

![NVIDIA 裝置狀態](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)

* 針對 NVIDIA Tesla GPU 建置 GPU 加速應用程式的開發人員也可以下載及安裝 [CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads)。



