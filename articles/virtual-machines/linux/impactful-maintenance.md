---
title: "對於 Azure 中的 Linux VM 進行的維護造成 VM 重新啟動 | Microsoft Docs"
description: "Linux 虛擬機器的維護造成 VM 重新啟動"
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.contentlocale: zh-tw
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>維護造成 VM 重新啟動

有時會因為基礎結構的規劃維護而重新您的 VM。 下列是可供您使用的功能，這些功能會影響可否此用 Azure 中代管的 VM︰

-   通知會至少在影響前的 30 天傳送。

-   每個 VM 的維護期間可見性。

-   對於影響 VM 的維護設定確切時間的彈性和控制。

Microsoft Azure 中的維護是排定在反覆項目中。 初始反覆項目具有較小的範圍，以降低風險參與推出全新的修正程式和功能。 後期反覆項目可能會跨越多個區域 (絕不會從相同的區域配對進行)。 VM 包含在單一維護反覆項目。 如果反覆項目已中止，剩餘的 VM 會包含在其他日後的反覆項目中。

規劃的維護反覆項目有兩個階段︰先佔式維護期間和排程維護期間。

**先佔式維護期間**可在您的 VM 上彈性進行維護。 如此一來，您可以判斷您的 VM 何時受到影響、更新的結果，以及每次維護 VM 相隔的時間。 您可以查詢每個 VM 查看是否已規劃進行維護，並檢查最後發出的維護要求所產生的結果。

**排程維護期間**是 Azure 排程您的 VM 進行維護的時間。 在先佔式維護期間之後的這段期間，您仍然可以查詢維護期間，但是無法再協調維護。

## <a name="availability-considerations-during-planned-maintenance"></a>規劃維護期間的可用性考量 

### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 Azure 在執行維護作業時，只會更新區域配對中單一區域的虛擬機器執行個體。 舉例來說，Azure 在更新美國中北部的虛擬機器時，就不會同時更新任何在美國中南部的虛擬機器。 這兩次更新作業會分別排定在不同的時間，以啟用區域之間的容錯移轉或負載平衡功能。 不過，像是北歐等其他區域可以和美國東部一同進行維護。
深入了解 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>單一執行個體 VM 相較於可用性設定組或 VM 擴展集

在 Azure 中使用虛擬機器部署工作負載時，您可以在可用性設定組中建立 VM，以便為應用程式提供高可用性。 這項組態可以確保在中斷或維護事件期間，至少有一部虛擬機器可以使用。

在可用性集合中，個別 VM 散佈在多達 20 個更新網域。 在規劃維護期間，在任何指定時間只有單一更新網域受影響。 在規劃維護期間，更新網域受影響的順序可能不是循序進行。 對於單一執行個體 VM (不屬於可用性設定組)，無法預測或判定哪個和哪些 VM 受影響。

虛擬機器擴展集是一個可讓您以單一資源的形式，部署和管理一組相同 VM 的 Azure 計算資源。
擴展集提供與更新網域形式的可用性設定組類似的保證。 

如需設定虛擬機器以取得高可用性的詳細資訊，請參閱[*管理 Windows 虛擬機器的可用性*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="scheduled-events"></a>排定的事件

Azure 中繼資料服務可讓您探索 Azure 中裝載的虛擬機器相關資訊。 排定的事件 (其中一個公開的類別) 會呈現即將發生的事件 (例如，重新開機) 相關資訊，您的應用程式才能做好準備以及限制中斷。

如需排定的事件有關的詳細資訊，請參閱 [Azure 中繼資料服務 - 排定的事件](../virtual-machines-scheduled-events.md)。

## <a name="maintenance-discovery-and-notifications"></a>維護探索和通知

個別 VM 層級的客戶可看見維護排程。 您可以使用 Azure 入口網站、API、PowerShell 或 CLI 查詢先佔式和排程維護期間。 此外，一個 (或多個) VM 在程序期間受影響時，應該會收到通知 (電子郵件)。

先佔式維護和排程維護階段開始時，會發出通知。 每次 Azure 訂閱應該會收到單一通知。 通知預設是傳送給訂閱的管理員和共同管理員。 您也可以設定維護通知的對象。

### <a name="view-the-maintenance-window-in-the-portal"></a>在入口網站中檢視維護期間 

您可以使用 Azure 入口網站尋找已排定進行維護的 VM。

1.  登入 Azure 入口網站。

2.  按一下並開啟 [虛擬機器] 刀鋒視窗。

3.  按一下 [資料行] 按鈕開啟可用資料行的清單。

4.  選取並新增 [維護期間]  資料行。 已排定進行維護的 VM 都會呈現維護期間。 一旦維護完成或中止，則維護期間將不再顯示。

### <a name="query-maintenance-details-using-the-azure-api"></a>使用 Azure API 查詢維護詳細資料

使用[取得 VM 資訊 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get)，並尋找執行個體檢視來探索個別的 VM 上的維護詳細資料。 回應包含下列項目︰

  - isCustomerInitiatedMaintenanceAllowed︰指出是否可以立即在 VM 上起始先佔式重新部署。

  - preMaintenanceWindowStartTime︰先佔式維護期間的開始時間。

  - preMaintenanceWindowEndTime︰ 先佔式維護期間的結束時間。 此後，您再也無法在此 VM 上起始維護。
    
  - maintenanceWindowStartTime︰您的 VM 受影響時排定維護期間的開始時間。

  - maintenanceWindowEndTime︰排定維護期間的結束時間。
  
  - lastOperationResultCode︰最後一個維護重新部署作業的結果。
 
  - lastOperationMessage︰描述您上次維護重新部署作業結果的訊息。


## <a name="pre-emptive-redeploy"></a>先佔式重新部署

先佔式重新部署動作可彈性控制對 Azure 中的 VM 進行維護的時間。 規劃的維護先從先佔式維護期間開啟，您可決定每個 VM 重新啟動的確切時間。 以下是使用這類功能的使用案例︰

-   需要與一般客戶協調維護。

-   Azure 提供的排定維護期間並不足夠。
    該段期間可能剛好是一週最忙碌的時間或時間太長。

-   對於多個執行個體或多層式應用程式，要兩個 VM 或後續的特定序列之間需要有足夠的時間。

在 VM 上呼叫先佔式重新部署時，會將 VM 移到已更新的結果，然後重新開啟它的電源，保留所有組態選項和相關聯的資源。 這麼做的時候，暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。

每個 VM 會啟用先佔式重新部署一次。 如果處理程序期間發生錯誤，作業會中止，VM 不會受影響，而且會被排除在規劃維護反覆項目之外。 稍後會有新的排程連絡您，讓您有機會對於您的 VM 造成的影響設定排程及排序。

