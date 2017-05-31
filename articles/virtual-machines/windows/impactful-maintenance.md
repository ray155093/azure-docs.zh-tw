---
title: "Azure 中具影響力的 Windows VM 維護 | Microsoft Docs"
description: "Windows 虛擬機器具影響力的維護。"
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
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b95a5bd216a6d9cdd6ae6ba8e849d0bf4be71fe5
ms.contentlocale: zh-tw
ms.lasthandoff: 04/03/2017


---

# <a name="impactful-maintenance-for-virtual-machines"></a>虛擬機器具影響力的維護

有時系統會因為要對基礎結構進行規劃維護，而將您的 VM 重新開機。 對於會對 Azure 中託管的 VM 可用性造成影響，下列是現在可供您使用的功能：

-   系統至少會在受到影響的 30 天前傳送通知。

-   每個 VM 在每次維護期間的可見性。

-   可彈性設定並控管會影響 VM 的確切維護時間。

Microsoft Azure 中的維護會以反覆項目排程。 初始反覆項目具有較小的範圍，以降低推出全新修正程式和功能所牽涉到的風險。 後期反覆項目可能會跨越多個區域 (絕不會從相同的區域配對進行)。 VM 包含在單一維護反覆項目。 如果反覆項目已中止，剩餘的 VM 會包含在其他日後的反覆項目中。

規劃的維護反覆項目有兩個階段：先佔式維護期間和排程維護期間。

**先佔式維護期間**可讓您在 VM 上彈性起始維護。 如此一來，您可以判斷 VM 何時受到影響、更新的結果，以及每次維護 VM 相隔的時間。 您可以查詢每個 VM 來查看是否已規劃進行維護，並檢查上次所起始維護要求的結果。

**排程維護期間**是 Azure 排程您的 VM 進行維護的時間。 在先佔式維護期間之後的這段期間，您仍然可以查詢維護期間，但是無法再協調維護。

## <a name="availability-considerations-during-planned-maintenance"></a>規劃維護期間的可用性考量 

### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 Azure 在執行維護作業時，只會更新區域配對中單一區域的虛擬機器執行個體。 舉例來說，Azure 在更新美國中北部的虛擬機器時，就不會同時更新任何在美國中南部的虛擬機器。 這兩次更新作業會分別排定在不同的時間，以啟用區域之間的容錯移轉或負載平衡功能。 不過，像是北歐等其他區域可以和美國東部一同進行維護。
深入了解 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>單一執行個體 VM 與可用性設定組或 VM 擴展集

在 Azure 中使用虛擬機器部署工作負載時，您可以在一個可用性設定組內建立 VM，以便為應用程式提供高可用性。 這項組態可以確保在中斷或維護事件期間，至少有一部虛擬機器可供使用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域。 在規劃維護期間，在任何指定時間只有單一更新網域受影響。 在規劃維護期間，受影響更新網域的順序可能不會循序進行。 對於單一執行個體 VM (不屬於可用性設定組)，無法預測或判定哪個和有多少 VM 會受到影響。

虛擬機器擴展集是一個可讓您以單一資源的形式，部署和管理一組相同 VM 的 Azure 計算資源。
擴展集提供與更新網域形式的可用性設定組類似的保證。 

如需設定虛擬機器以取得高可用性的詳細資訊，請參閱[*管理 Windows 虛擬機器的可用性*](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="scheduled-events"></a>排程的事件

Azure 中繼資料服務可讓您探索 Azure 中裝載的虛擬機器相關資訊。 排定的事件 (其中一個公開的類別) 會呈現即將發生的事件 (例如，重新開機) 相關資訊，您的應用程式才能做好準備以及限制中斷。

如需排程的事件的詳細資訊，請參閱 [Azure 中繼資料服務 - 排程的事件](../virtual-machines-scheduled-events.md)。

## <a name="maintenance-discovery-and-notifications"></a>維護探索和通知

客戶可看見個別 VM 層級的維護排程。 您可以使用 Azure 入口網站、API、PowerShell 或 CLI 查詢先佔式和排程維護期間。 此外，一個 (或多個) VM 在程序期間受影響時，應該會收到通知 (電子郵件)。

先佔式維護和排程維護階段開始時會發出通知。 預期每個 Azure 訂用帳戶會收到單一通知。 通知預設是傳送給訂用帳戶的管理員和共同管理員。 您也可以設定維護通知的對象。

### <a name="view-the-maintenance-window-in-the-portal"></a>在入口網站中檢視維護期間 

您可以使用 Azure 入口網站並尋找已排程維護的 VM。

1.  登入 Azure 入口網站。

2.  按一下並開啟 [虛擬機器] 刀鋒視窗。

3.  按一下 [資料行] 按鈕可開啟可供選擇資料行的清單

4.  選取並新增 [維護期間] 資料行。 已排程維護的 VM 都會呈現維護期間。 一旦維護完成或中止，則維護期間將不再顯示。

### <a name="query-maintenance-details-using-the-azure-api"></a>使用 Azure API 查詢維護詳細資料

使用[取得 VM 資訊 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get)，並尋找執行個體檢視來探索個別 VM 上的維護詳細資料。 回應包含下列元素：

  - isCustomerInitiatedMaintenanceAllowed：指出是否可以立即在 VM 上起始先佔式重新部署。

  - preMaintenanceWindowStartTime：先佔式維護期間的開始時間。

  - preMaintenanceWindowEndTime：先佔式維護期間的結束時間。 此後，您再也無法在此 VM 上起始維護。
    
  - maintenanceWindowStartTime：您的 VM 受影響時排程維護期間的開始時間。

  - maintenanceWindowEndTime：排程維護期間的結束時間。
  
  - lastOperationResultCode：上次維護重新部署作業的結果。
 
  - lastOperationMessage：描述您上次維護重新部署作業結果的訊息。

## <a name="pre-emptive-redeploy"></a>先佔式重新部署

先佔式重新部署動作可讓您彈性控制對 Azure 中的 VM 套用維護的時間。 規劃的維護從先佔式維護期間開始，在此您可以決定每個 VM 重新開機的確切時間。 以下是這類功能很實用的使用案例：

-   需要與一般客戶協調維護。

-   Azure 提供的排定維護期間並不足夠。
    該段期間可能剛好是一週最忙碌的時間或時間太長。

-   對於多個執行個體或多層式應用程式，兩個 VM 需要有足夠的時間或遵循特定的順序。

當您呼叫 VM 上的先佔式重新部署時，它會將 VM 移至已更新的節點，並接著將它的電源重新開啟，保留所有組態選項和相關聯的資源。 這麼做的時候，暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。

系統會對每個 VM 啟用一次先佔式重新部署。 如果在程序期間發生錯誤，作業就會中止，不會影響 VM 並將它排除在規劃的維護反覆項目之外。 稍後有新的排程時會連絡您，讓您有新的機會對您的 VM 排程及排序相關的影響。
