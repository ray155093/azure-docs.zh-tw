---
title: "Azure 中 Windows VM 的可用性設定組 | Microsoft Docs"
description: "了解適合用來在 Azure 基礎結構服務中部署可用性設定組的關鍵設計和實作指導方針。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f9449f58-664b-4d5d-82f6-84c5d083047f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 2d146d3a3bd2497a6c1d9b2b924d2e2cd00710de
ms.lasthandoff: 04/14/2017


---
# <a name="azure-availability-sets-guidelines-for-windows-vms"></a>適用於 Windows VM 的 Azure 可用性設定組指導方針

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文著重於了解可用性設定組的必要計畫步驟，以確保應用程式在計畫或非計畫的事件發生期間可以維持存取性。

## <a name="implementation-guidelines-for-availability-sets"></a>可用性設定組的實作指導方針
决策：

* 在您的應用程式基礎結構中，需要針對各種角色和層級使用多少個可用性設定組？

工作：

* 定義每個所需應用程式層級中的 VM 數目。
* 決定您是否需要調整應用程式所使用的錯誤數目或更新網域。
* 使用您的命名慣例，來定義必要的可用性設定組以及要置於其中的 VM。 一個可用性設定組中只能放置一個 VM。 

## <a name="availability-sets"></a>可用性集合
在 Azure 中，虛擬機器 (VM) 可以被放置在稱為可用性設定組的邏輯群組之中。 當您在可用性設定組中建立 VM 時，Azure 平台會將這些 VM 的位置散佈於基礎結構上。 若 Azure 平台發生預計的維護事件，或是發生基礎硬體 / 基礎結構錯誤，使用可用性設定組將可確保至少有一個 VM 會保持執行。

最佳作法是不將應用程式放置在單一 VM 上。 包含單一 VM 的可用性設定組將無法在 Azure 平台內針對計畫或非計畫的事件獲得保護。 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 需要可用性設定組內有兩個或以上的 VM，以允許將 VM 散佈於基礎結構上。 如果您使用的是 [Azure 進階儲存體](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，Azure SLA 就會套用到單一 VM。

Azure 中的基礎結構分為多個硬體叢集。 每個硬體叢集皆可支援某個 VM 大小範圍。 不論何時，可用性設定組都只能裝載在單一硬體叢集上。 因此，能夠存在於單一可用性設定組中的 VM 大小範圍會限制在硬體叢集所支援的 VM 大小範圍。 當部署可用性設定組中的第一個 VM，或啟動所有 VM 目前都處於「已停止-已解除配置」狀態之可用性設定組中的第一個 VM 時，就會選取該可用性設定組的硬體叢集。 下列 PowerShell 命令可用來判斷可用性設定組可用的 VM 大小範圍："Get-AzureRmVMSize -ResourceGroupName \<string\> -AvailabilitySetName \<string\>"

每個硬體叢集都分為多個更新網域和容錯網域。 這些網域是會根據主機是共用一般更新週期，或共用類似的實體基礎結構 (例如電源和網路功能) 來定義。 Azure 會自動將您位於可用性設定組內的 VM 散佈於網域上，以維護可用性和容錯。 根據應用程式的大小，以及可用性設定組內的 VM 數目，您可以調整想要使用的網域數目。 您可以深入了解[管理更新和容錯網域的可用性及使用](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

設計應用程式基礎結構時，計畫要使用的應用程式層級。 將用途相同的 VM 分組為可用性設定組，例如由執行 IIS 的前端 VM 所組成的可用性設定組。 為執行 SQL Server 的後端 VM 建立個別的可用性設定組。 我們的目標是要確保應用程式的每個元件都會受到某個可用性設定組所保護，且至少有一個執行個體總是保持執行。

負載平衡器可以在每個應用程式層級之前運用，以和可用性設定組一起運作，並確保流量總是會被路由到正在執行中的執行個體。 若沒有負載平衡器，您的 VM 可能會在規劃或未規劃的維護事件期間持續執行，但在主要 VM 無法使用的情況下，您的使用者可能無法解析它們。

針對儲存層的高可用性設計應用程式。 最佳作法是[對於可用性設定組中的 VM 使用受控磁碟](../windows/manage-availability.md#use-managed-disks-for-vms-in-availability-set)。 如果您目前使用非受控磁碟，強烈建議您[將可用性設定組中的 VM 轉換為受控磁碟](../windows/convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


