---
title: "Service Fabric 程式撰寫模型概觀 | Microsoft Docs"
description: "Service Fabric 提供兩種架構來建置服務：動作項目架構和服務架構。 它們在簡化與控制中提供不同的取捨。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 102ede8d2aafaf485a5212faad47de6781d84578
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017


---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric 程式設計模型概觀
Service Fabric 提供多種撰寫和管理服務的方式。 服務可選擇使用 Service Fabric API 以善加運用平台的功能和應用程式架構。 服務也可以是以任何語言撰寫的任何已編譯可執行程式，或是在 Service Fabric 叢集所裝載之容器中執行的程式碼。

### <a name="guest-executables"></a>客體可執行檔
[客體可執行檔](service-fabric-deploy-existing-app.md)是以任何語言所撰寫，且與其他服務一同裝載於 Service Fabric 叢集的現有任意可執行檔。 客體可執行檔未直接與 Service Fabric API 整合。 不過，它們仍然受惠於功能和平台提供項目，例如自訂健康情況和負載報告，以及透過呼叫 REST API 來探索服務。 它們也具備完整的應用程式生命週期支援。

部署您的第一個 [來賓可執行的應用程式](service-fabric-deploy-existing-app.md)，開始使用來賓可執行檔。

### <a name="containers"></a>容器
根據預設，Service Fabric 會以處理程序形式部署和啟用這些服務。 Service Fabric 也可以在[容器](service-fabric-containers-overview.md)中部署服務。 Service Fabric 支援在 Windows Server 2016 上部署 Linux 容器和 Windows 容器。 您可以將現有的應用程式、無狀態服務或具狀態服務部署在容器中，並且可以在同一個應用程式中混合使用處理序中的服務和容器中的服務。

## <a name="reliable-services"></a>Reliable Services
Reliable Services 是輕量級的服務撰寫架構，這些服務與 Service Fabric 平台整合，並得益於完整的平台功能。 Reliable Services 提供最基本的 API 集合，允許 Service Fabric 執行階段管理服務的生命週期，也允許服務與執行階段互動。 應用程式架構最為精簡，讓您完整掌控設計和實作選擇，而且可用來裝載任何其他應用程式架構，例如 ASP.NET Core。

Reliable Services 與大多數服務平台 (例如 Web 伺服器) 一樣，可以是無狀態的，其中每個服務執行個體都是以平等方式建立，並且狀態保存在外部解決方案中，例如 Azure DB 或「Azure 資料表儲存體」。

Reliable Services 也可以是具狀態且為 Service Fabric 專有，其狀態使用 Reliable Collections 直接保存在服務本身中。 狀態透過複寫變得高度可用，並透過資料分割散發，全由 Service Fabric 自動管理。

[深入了解 Reliable Services](service-fabric-reliable-services-introduction.md)或從[撰寫第一個 Reliable Services](service-fabric-reliable-services-quick-start.md) 開始。

## <a name="reliable-actors"></a>Reliable Actors
Reliable Actor 架構是建置在 Reliable Services 最上層的應用程式架構，其根據動作項目設計模式來實作 Virtual Actor 模式。 Reliable Actor 架構使用獨立的計算單位，和以單一執行緒方式執行稱為動作項目的狀態。 Reliable Actor 架構提供動作項目的內建通訊和預先設定狀態持續性和相應放大組態。

Reliable Actors 本身是建置在 Reliable Services 上的應用程式架構，與 Service Fabric 平台完全整合，得益於平台提供的完整功能集。

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric 與 [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 整合，可用來建置 Web 和 API 應用程式。 

## <a name="next-steps"></a>後續步驟
[深入了解 Reliable Actors](service-fabric-reliable-actors-introduction.md) 或從[撰寫您的第一個 Reliable Actor 服務](service-fabric-reliable-actors-get-started.md)開始著手
[深入了解如何在 Windows 或 Linux 中將服務容器化](service-fabric-deploy-container.md)
[使用 ASP.NET Core 來建置前端服務](service-fabric-add-a-web-frontend.md)



