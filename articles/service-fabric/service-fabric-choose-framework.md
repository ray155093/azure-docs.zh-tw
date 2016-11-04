---
title: Service Fabric 程式撰寫模型概觀 | Microsoft Docs
description: Service Fabric 提供兩種架構來建置服務：動作項目架構和服務架構。它們在簡化與控制中提供不同的取捨。
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/18/2016
ms.author: seanmck

---
# Service Fabric 程式設計模型概觀
Service Fabric 提供多種撰寫和管理服務的方式。服務可以選擇使用 Service Fabric API，充分利用平台功能和應用程式架構，或者服務可以僅僅是以任何語言撰寫、只裝載在 Service Fabric 叢集上的任何已編譯的可執行程式。

## 來賓可執行檔
來賓可執行檔是以任何語言撰寫的任意可執行檔，所以您可以將現有的應用程式裝載在 Service Fabric 叢集上。來賓可執行檔可以封裝在應用程式，和其他服務裝載在一起。Service Fabric 會處理可執行檔的協調流程和簡易執行管理，確保它根據服務描述啟動執行。不過，因為來賓可執行檔不直接與 Service Fabric API 整合，所以它們不會得益於平台提供的完整功能，例如自訂的健康狀態和負載報告、服務端點註冊，以及具狀態計算。

部署您的第一個[來賓可執行的應用程式](service-fabric-deploy-existing-app.md)，開始使用來賓可執行檔。

## Reliable Services
Reliable Services 是輕量級的服務撰寫架構，這些服務與 Service Fabric 平台整合，並得益於完整的平台功能。Reliable Services 提供最基本的 API 集合，允許 Service Fabric 執行階段管理服務的生命週期，也允許服務與執行階段互動。應用程式架構是最基本的，讓您完整控制設計和實作選擇，而且可用來裝載任何其他應用程式架構，如 ASP.NET MVC 或 Web API。

Reliable Services 可以是無狀態的，就像大部分的服務平台一樣，如 Azure 雲端服務的 Web 伺服器或背景工作角色，服務的每個執行個體都平等建立，狀態保存在外部方案中，如 Azure DB 或 Azure 表格儲存體。

Reliable Services 也可以是具狀態且為 Service Fabric 專有，其狀態使用 Reliable Collections 直接保存在服務本身中。狀態透過複寫變得高度可用，並透過資料分割散發，全由 Service Fabric 自動管理。

[深入了解 Reliable Services](service-fabric-reliable-services-introduction.md) 或從[撰寫第一個 Reliable Services](service-fabric-reliable-services-quick-start.md) 開始。

## Reliable Actors
Reliable Actor 架構是建置在 Reliable Services 最上層的應用程式架構，其根據動作項目設計模式來實作 Virtual Actor 模式。Reliable Actor 架構使用獨立的計算單位，和以單一執行緒方式執行稱為動作項目的狀態。Reliable Actor 架構提供動作項目的內建通訊和預先設定狀態持續性和相應放大組態。

Reliable Actors 本身是建置在 Reliable Services 上的應用程式架構，與 Service Fabric 平台完全整合，得益於平台提供的完整功能集。

## 後續步驟
[深入了解 Reliable Actors](service-fabric-reliable-actors-introduction.md) 或從[撰寫第一項 Reliable Actor 服務](service-fabric-reliable-actors-get-started.md)開始。

<!---HONumber=AcomDC_0720_2016-->