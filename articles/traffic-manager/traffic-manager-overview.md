---
title: "什麼是流量管理員 | Microsoft Docs"
description: "本文將協助您了解何謂流量管理員，以及它是否為您的應用程式的正確流量路由選擇"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>流量管理員概觀

Microsoft Azure 流量管理員可讓您控制使用者流量，將流量分散到不同資料中心的服務端點。 流量管理員支援的服務端點包括 Azure VM、Web Apps 和雲端服務。 您也可以將流量管理員使用於外部、非 Azure 端點。

流量管理員會使用網域名稱系統 (DNS)，根據[流量路由方法](traffic-manager-routing-methods.md)和端點的健全狀況，將用戶端要求導向到最適當的端點。 流量管理員提供各種流量路由方法，以符合不同的應用程式需求、端點健全狀況[監視](traffic-manager-monitoring.md)、及自動容錯移轉。 流量管理員可針對失敗彈性應變，包括整個 Azure 區域失敗。

## <a name="traffic-manager-benefits"></a>流量管理員的優點

流量管理員可協助您：

* **提高重要應用程式的可用性**

    流量管理員藉由監視端點，以及在端點停止運作時提供自動容錯移轉功能，為重要的應用程式提供高可用性。

* **提升高效能應用程式的回應能力**

    Azure 可讓您在世界各地的資料中心內執行雲端服務或網站。 流量管理員藉由將流量導向用戶端網路延遲最低的端點，改善應用程式的回應性。

* **在不需要停機的情況下執行服務維護**

    您可以在您的應用程式執行規劃的維護作業，而不需要停機。 當維護正在進行時，流量管理員會將流量導向替代的端點。

* **結合內部部署和雲端應用程式**

    流量管理員支援外部、非 Azure 端點，並可搭配混合式雲端和內部部署使用，包括「高載至雲端」、「移轉至雲端」和「容錯移轉至雲端」案例。

* **大型複雜部署的流量分配**

    利用[巢狀的流量管理員設定檔](traffic-manager-nested-profiles.md)，可以組合流量路由方法，建立複雜且彈性的規則，以支援更大型且更複雜部署的需求。

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>後續步驟

* 深入了解 [流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。
* 了解如何使用 [流量管理員端點監視](traffic-manager-monitoring.md)來開發高可用性應用程式。
* 深入了解流量管理員支援的 [流量路由方法](traffic-manager-routing-methods.md) 。
* [建立流量管理員設定檔](traffic-manager-manage-profiles.md)。



<!--HONumber=Nov16_HO3-->


