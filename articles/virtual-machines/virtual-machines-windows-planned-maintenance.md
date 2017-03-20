---
title: "Windows VM 預定進行的維修 | Microsoft Docs"
description: "了解什麼是 Azure 預定進行的維修，以及其對 Azure 中執行的 Windows 虛擬機器的影響為何。"
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecc7666f80f7a9a5b2f8daaa65fd1766616fc85
ms.lasthandoff: 03/03/2017


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Azure 中虛擬機器預定進行的維修
了解什麼是 Azure 預定進行的維修，以及其對 Windows 虛擬機器可用性的影響為何。 本文也適用於 [Linux 虛擬機器](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文提供 Azure 計劃性維護程序的背景。 如果您想要針對 VM 重新啟動的原因進行疑難排解，您可以 [閱讀這篇部落格文章，其內容詳細說明檢視 VM 重新啟動記錄檔](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Azure 執行計劃性維護的原因
為提升虛擬機器之基礎主機基礎結構的可靠性、效能和安全性，Microsoft Azure 會全球定期執行更新。 許多這些更新在執行時並不會對虛擬機器或雲端服務造成任何影響，包括記憶體保留的更新。

不過，有些更新的確需要將虛擬機器重新開機，才能將必要更新套用至基礎結構。 我們會在修補基礎結構時將虛擬機器關機，然後再將虛擬機器重新啟動。

請注意，有兩種類型的維護會影響虛擬機器的可用性：計劃性與非計劃性。 本頁面說明 Microsoft Azure 會如何執行計劃性維護。 如需非計劃性維護的詳細資訊，請參閱[了解計劃性與非計劃性維護](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

