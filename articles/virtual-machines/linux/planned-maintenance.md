---
title: "Azure 中 Linux VM 的計劃性維護 | Microsoft Docs"
description: "了解什麼是 Azure 計劃性維護，以及它對在 Azure 中執行的 Windows 虛擬機器的影響為何。"
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: zh-tw
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Linux 虛擬機器的計劃性維護 

為提升虛擬機器之基礎主機基礎結構的可靠性、效能和安全性，Microsoft Azure 會全球定期執行更新。 這些更新的範圍，從在主控環境 (OS、Hypervisor 和各種在主機上部署的代理程式) 中修補軟體元件，一路涵蓋到升級網路元件，以及硬體解除委任。

這些更新大多數都會在不影響託管的虛擬機器或雲端服務的情況下執行。

不過，更新在某些情況下確實會對託管的虛擬機器造成影響：

-   使用就地 VM 移轉的 VM 保留維護，會描述一種虛擬機器在維護期間不會重新啟動的更新類別。

-   需要重新啟動或重新部署至託管虛擬機器的 VM 重新啟動維護。

請注意，本頁面是說明 Microsoft Azure 執行計劃性維護的方式。 如需非計劃性事件 (中斷) 的詳細資訊，請參閱[管理虛擬機器的可用性](../windows/manage-availability.md)。
