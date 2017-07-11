---
title: "透過入口網站啟用 Azure 事件中樞擷取功能 | Microsoft Docs"
description: "使用 Azure 入口網站啟用事件中樞擷取功能。"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017


---

<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

# 使用 Azure 入口網站啟用事件中樞擷取功能

您可以使用 [Azure 入口網站](https://portal.azure.com)，在建立事件中樞時設定擷取功能。 按一下 [建立事件中樞] 入口網站刀鋒視窗中的 [開啟] 按鈕，即可啟用擷取功能。 然後按一下刀鋒視窗的 [容器]  區段，就可以設定儲存體帳戶和容器。 事件中樞擷取會對儲存體使用服務對服務驗證，因此您不需要指定儲存體連接字串。 資源選擇器會自動選取儲存體帳戶的資源 URI。 如果您使用 Azure Resource Manager，您必須以字串形式明確提供此 URI。

預設時間範圍為 5 分鐘。 最小值是 1，最大值是 15。 **大小** 範圍為 10-500 MB。

![][1]

<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

## 將擷取功能新增至現有事件中樞

您可以在位於事件中樞命名空間的現有事件中樞上設定擷取功能。 此功能不適用於舊版的**傳訊**或**混合**類型命名空間。 若要在現有事件中樞上啟用擷取功能，或變更您的擷取設定，請按一下命名空間以載入 [基本資訊] 刀鋒視窗，然後按一下您要啟用或變更擷取設定的事件中樞。 最後，按一下開啟之刀鋒視窗的 [屬性]  區段，如下圖所示：

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

<a id="next-steps" class="xliff"></a>

## 後續步驟

您也可以透過 Azure Resource Manager 範本來設定事件中樞擷取。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本啟用擷取功能](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。

