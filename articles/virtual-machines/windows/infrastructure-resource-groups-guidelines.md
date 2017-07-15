---
title: "Azure 中 Windows VM 的資源群組 | Microsoft Docs"
description: "了解適合用來在 Azure 基礎結構服務中部署資源群組的關鍵設計和實作指導方針。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0fbcabcd-e96d-4d71-a526-431984887451
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 3d915f430e323f2b2ad55773f2028a3d7dbf2bcf
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# 適用於 Windows VM 的 Azure 資源群組指導方針
<a id="azure-resource-group-guidelines-for-windows-vms" class="xliff"></a>

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

本文著重於了解如何在資源群組中以邏輯方式建置環境，並將所有元件分組的方式。

## 資源群組的實作指導方針
<a id="implementation-guidelines-for-resource-groups" class="xliff"></a>
决策：

* 您要以核心基礎結構元件建置資源群組，還是以完整的應用程式部署？
* 您是否需要使用角色型存取控制來限制資源群組的存取？

工作：

* 定義核心基礎結構元件，以及您所需的專屬資源群組。
* 檢閱如何實作 Resource Manager 範本以取得一致且可重現的部署。
* 定義針對控制資源群組存取所需的使用者存取角色。
* 使用您的命名慣例來建立資源群組組合。 您可以使用 Azure PowerShell 或入口網站。

## 資源群組
<a id="resource-groups" class="xliff"></a>
在 Azure 中，您可以邏輯方式將相關資源 (例如儲存體帳戶、虛擬網路及虛擬機器 (VM)) 分組，以將它們當成單一實體進行部署、管理及維護。 從管理的角度來看，這個方式可讓您在將所有相關資源保持在一起的情況下，更輕鬆地部署應用程式，或是授與其他人存取該資源群組。 資源群組名稱長度最多可以有 90 個字元。 如需針對資源群組取得更完整的了解，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。

資源群組的一項主要功能是能夠使用範本建置您的環境。 範本只是一個 JSON 檔案，可宣告儲存體、網路和計算資源。 您也可以定義任何要套用的相關自訂指令碼或設定。 透過使用這些範本，您可以為應用程式建立一致且可重現的部署。 這種方法讓您可以輕鬆建置開發環境，然後使用相同的範本建立生產環境部署，反之亦然。 如需深入了解範本的使用方式，請參閱 [範本逐步解說](../../azure-resource-manager/resource-manager-template-walkthrough.md) ，它將能引導您完成建置範本的每個步驟。

您在使用資源群組設計環境時，有兩種不同的執行方式：

* 適用於每個應用程式部署的資源群組，其中結合了儲存體帳戶、虛擬網路和子網路、VM、負載平衡器等項目。
* 集中式資源群組，其中包含您的核心虛擬網路和子網路或儲存體帳戶。 接著，您的應用程式會位於它們自己的資源群組中，其中只會包含 VM、負載平衡器、網路介面等項目。

隨著您相應放大，為虛擬網路和子網路建立集中式資源群組，將能讓您更輕鬆地為混合式連線能力選項建立跨單位網路連線。 另一個替代方式是讓每個應用程式自行擁有需要個別設定和維護的虛擬網路。  [角色型存取控制](../../active-directory/role-based-access-control-what-is.md) 提供更細微的資源群組存取控制方法。 針對實際執行應用程式，您可以控制可以存取那些資源的使用者，或是針對核心基礎結構資源限制只有基礎結構工程師可以使用它們。 您的應用程式使用者只能存取他們資源群組內的應用程式元件，而非環境的核心 Azure 基礎結構。 當您設計環境時，請考慮需要存取資源的使用者，並據以設計您的資源群組。 

## 後續步驟
<a id="next-steps" class="xliff"></a>
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


