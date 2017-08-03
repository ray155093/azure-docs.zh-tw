---
title: "Azure Linux VM 大小 - GPU | Microsoft Docs"
description: "列出 Azure 中可用的不同 Linux 虛擬機器 GPU 最佳化大小。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: ec289cf53f2cfecd2744b739667ef831dafd59a4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---

# <a name="gpu-linux-vm-sizes"></a>GPU Linux VM 大小

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

如需驅動程式安裝和驗證步驟，請參閱 [Linux 的 N 系列驅動程式安裝](n-series-driver-setup.md)。

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* 不建議您在 Ubuntu NC VM 上安裝 X 伺服器或其他使用 nouveau 驅動程式的系統。 安裝 NVIDIA GPU 驅動程式之前，您必須停用 nouveau 驅動程式。  

## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [高效能計算](sizes-hpc.md)

## <a name="next-steps"></a>後續步驟
深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。
