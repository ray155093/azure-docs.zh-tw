<properties 
   pageTitle="什麼是流量管理員 | Microsoft Azure"
   description="本文將協助您了解何謂流量管理員，以及它是否為您的應用程式的正確流量路由選擇"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="sewhee" />

# 什麼是流量管理員？

Microsoft Azure 流量管理員可讓您控制使用者流量，將流量分散到您在世界各地的不同資料中心執行的服務端點。

流量管理員支援的服務端點包括 Azure VM、Web Apps 和雲端服務。您也可以將流量管理員使用於外部、非 Azure 端點。

流量管理員的運作方式是使用網域名稱系統 (DNS)，根據所設定的流量路由方法和目前的端點健康狀態檢視，將使用者要求導向最適當的端點。然後用戶端就會直接連接到適當的服務端點。

流量管理員支援[各種的流量路由方法](traffic-manager-routing-methods.md)，以符合不同的應用程式需求。流量管理員提供[端點健康情況檢查和自動端點容錯移轉](traffic-manager-monitoring.md)，讓您建置可從失敗 (包括整個 Azure 區域的失敗) 中復原的高可用性應用程式。

## 流量管理員的優點

流量管理員可協助您：

- **提高重要應用程式的可用性** – 流量管理員可讓您藉由監視 Azure 中的端點，以及在端點停止運作時提供自動容錯移轉功能，為重要的應用程式提供高可用性。
- **改善高效能應用程式的回應能力** – Azure 可讓您在位於世界各地的資料中心執行雲端服務或網站。流量管理員可以藉由將使用者導向用戶端網路延遲最低的端點，改善應用程式的回應性。
- **升級和執行服務維護而不需要停機** – 您可以在進行維護時，使用流量管理員將流量導向替代端點，順暢地對應用程式執行升級及其他計劃性維護作業，而不需要使用者停機。
- **結合內部部署和雲端應用程式** – 流量管理員支援外部、非 Azure 端點，並可搭配混合式雲端和內部部署使用，包括「高載至雲端」、「移轉至雲端」和「容錯移轉至雲端」案例。
- **分散大型複雜部署的流量** – 使用[巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)可以合併流量路由方法，以建立複雜而有彈性的流量路由組態，進而符合更大型、更複雜部署的需求。

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## 後續步驟

- 深入了解[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。

- 了解如何使用[流量管理員端點監視](traffic-manager-monitoring.md)來開發高可用性應用程式。

- 深入了解流量管理員支援的[流量路由方法](traffic-manager-routing-methods.md)。

- [建立流量管理員設定檔](traffic-manager-manage-profiles.md)。
 

<!---HONumber=AcomDC_0824_2016-->