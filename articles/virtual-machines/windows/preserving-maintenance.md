---
title: "Azure 中 Windows VM 的 VM 保留維護 | Microsoft Docs"
description: "針對記憶體保留更新的就地 VM 移轉。"
services: virtual-machines-windows
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 8888bafbc3aba24168312b611a9b4fbde25f376d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>搭配就地 VM 移轉的 VM 保留維護

雖然大多數的更新對託管的 VM 都不會有影響，在某些情況下，針對元件或服務的更新 (在虛擬機器沒有完整重新開機的情況下) 會對正在執行的 VM 造成微量的干擾。

這些更新是透過可進行就地即時移轉 (也稱為「記憶體保留更新」) 的技術來完成。 更新主機時，虛擬機器會進入「暫停」的狀態，並將記憶體保留在 RAM 中，而主控環境 (例如基礎作業系統) 則會在此期間套用必要的更新和修補程式。
虛擬機器會在暫停後 30 秒內繼續執行。
繼續執行後，系統將會自動同步化虛擬機器的時鐘。

並非所有更新都可以透過這種機制來部署，但因為有短暫的暫停期間，以這種方式部署更新可大幅減少對虛擬機器的影響。

多重執行個體更新 (可用性設定組中的 VM) 一次只會套用到一個更新網域。

這些更新對某些應用程式的影響可能比對其他應用程式更大。 例如，執行即時事件處理、媒體串流處理或轉碼，或是高輸送量網路服務案例的應用程式，其設計可能不會容許暫停 30 秒。 在虛擬機器中執行的應用程式，可以透過呼叫 [Azure 中繼資料服務](../virtual-machines-instancemetadataservice-overview.md)的[排程的事件](../virtual-machines-scheduled-events.md) API，來了解即將發行的更新。

