
---
title: "Azure 中的 Windows VM 大小 | Microsoft Docs"
description: "列出 Azure 中 Windows 虛擬機器的不同可用大小。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 365d6ad9ec0e0a7ad8d9742d863540646257e298
ms.lasthandoff: 03/27/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Azure 中 Windows 虛擬機器的大小

本文說明可用於執行 Windows 應用程式與工作負載之 Azure 虛擬機器的可用大小及選項。 同時也提供當您規劃使用這些資源時所需注意的部署考量。  本文也適用於 [Linux 虛擬機器](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

> [!IMPORTANT]
>* 如需各式大小的定價，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)。 
>* 若要查看 Azure VM 的一般限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
>* 儲存體成本會分別根據儲存體帳戶中使用的頁面來計算。 如需詳細資料，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。
> * 深入了解 [Azure 計算單位 (ACU)](virtual-machines-windows-acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
>
>
<br>    




| 類型                     | 大小           |    說明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [一般用途](virtual-machines-windows-sizes-general.md)          | DSv2、Dv2、DS、D、Av2、A0-7 | CPU 與記憶體的比例平均。 適用於測試和開發、小型至中型資料庫，以及低至中流量 Web 伺服器。 |
| [計算最佳化](virtual-machines-windows-sizes-compute.md)        | Fs、F             | CPU 與記憶體的比例高。 適用於中流量 Web 伺服器、網路設備、批次處理，以及應用程式伺服器。        |
| [記憶體最佳化](virtual-machines-windows-sizes-memory.md)         | GS、G、DSv2、DS   | 記憶體與核心的比例高。 適用於關聯式資料庫伺服器、中型至大型快取，以及記憶體內部分析。                 |
| [儲存體最佳化](virtual-machines-windows-sizes-storage.md)        | Ls                | 高磁碟輸送量及 IO。 適用於巨量資料、SQL 及 NoSQL 資料庫。                                                         |
| [GPU](virtual-machines-windows-sizes-gpu.md)            | NV、NC            | 以大量圖形轉譯和視訊編輯為目標的特製化虛擬機器。 有單一或多個 GPU 可供使用。       |
| [高效能計算](virtual-machines-windows-sizes-hpc.md) | H、A8-11          | 速度最快、功能最強的 CPU 虛擬機器，搭載選配的高輸送量網路介面 (RDMA)。 

<br>

深入了解 [Azure 計算單位 (ACU)](virtual-machines-windows-acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

深入了解可用的不同 VM 大小：
- [一般用途](virtual-machines-windows-sizes-general.md)
- [計算最佳化](virtual-machines-windows-sizes-compute.md)
- [記憶體最佳化](virtual-machines-windows-sizes-memory.md)
- [儲存體最佳化](virtual-machines-windows-sizes-storage.md)
- [GPU 最佳化](virtual-machines-windows-sizes-gpu.md)
- [高效能計算](virtual-machines-windows-sizes-hpc.md)




