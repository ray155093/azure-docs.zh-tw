---
title: "定義及管理 Azure 微服務中的狀態 |Microsoft Docs"
description: "如何定義和管理 Service Fabric 中的服務狀態"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: d03bd6a4c317da67a4e6d0e8cdb0cbd3f07d5a1f


---
# <a name="service-state"></a>服務狀態
「服務狀態」係指服務正常運作所需的資料。 其包括可以讓服務讀取及寫入，以執行工作的資料結構及變數。

以計算機服務為例。 此服務會採用兩個數字並傳回其總和。 這是一種沒有與其相關聯資料的純粹無狀態服務。

現在，假設另有一個計算機服務，除了計算加總之外，也有方法可以傳回上一個計算總和。 此服務現在成為可設定狀態 - 其中包含某些要寫入 (當其計算新的總和) 和讀取 (當其傳回最後一個已計算總和) 的狀態。

在 Azure Service Fabric 中，第一個服務稱為無狀態服務。 第二個服務稱為可設定狀態的服務。

## <a name="storing-service-state"></a>儲存服務狀態
狀態可進行外部化，也可並存在運作該狀態的程式碼中。 狀態的外部化通常是使用外部資料庫或存放區來完成。 在計算機範例中，這可能是 SQL 資料庫，亦即目前儲存在資料表中的結果。 每個計算總和的要求會在此資料列上執行更新。

狀態也可以使用處理此程式碼的程式碼進行共置。 Service Fabric 中的具狀態服務即可使用此模型來建置。 Service Fabric 提供的基礎結構可確保在發生失敗時，此狀態仍具高可用性並會容錯。

## <a name="next-steps"></a>後續步驟
如需有關 Service Fabric 概念的詳細資訊，請參閱下列文章：

* [Service Fabric 服務的可用性](service-fabric-availability-services.md)
* [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)
* [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO4-->


