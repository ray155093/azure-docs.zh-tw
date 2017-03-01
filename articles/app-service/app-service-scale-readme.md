---
title: "Azure App Service：調整 App Service 應用程式"
description: "了解在 App Service 中調整應用程式的優缺點。"
keywords: "App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Azure App Service：調整 App Service 應用程式
在 Azure App Service 中裝載的應用程式可以達到最大規模 [Canadian Broadcasting Corporation/Radio-Canada leverage Azure for smooth election coverage (加拿大廣播公司/加拿大廣播電台運用 Azure 處理大選新聞)](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)。
不過，調整應用程式是很複雜的問題，沒有所謂的「一體適用」方案。 若要正確調整您的應用程式，下列 3 項要點可協助您順利進行：

1. 了解您的應用程式架構和其缺點。
   * 您的應用程式是否可設定狀態？ 沒有狀態？
   * 您的應用程式有哪些元件？
     * 應用程式的瓶頸在哪裡？
   * 當負載套用至您的應用程式時，會最先中斷哪個項目？
2. 了解預期的負載和效能需求。
   * 應用程式需要為一千個使用者或一百萬個使用者提供服務？
   * 流量是來自單一地區或全球？
   * 是否有季節性變化及流量尖峰？
   * 應用程式回應速度如何？ 1 秒？ 1 毫秒？
3. 了解及正確運用裝載您的應用程式的平台。
   * 為了達到我的規模的目標，應該利用哪些功能？

本節將有助您了解所有因素，並協助您設計可利用必要的 App Service 功能的策略，以達到延展性目標。

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]


