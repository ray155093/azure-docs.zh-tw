---
title: "定義及管理狀態 |Microsoft Docs"
description: "如何定義和管理 Service Fabric 中的服務狀態"
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bb27c2f2224791a6a30819da5ee0f09ad7bfa95c


---
# <a name="service-state"></a>服務狀態
**服務狀態** 是指服務正常運作所需的資料。 其包括可以讓服務讀取及寫入，以執行工作的資料結構及變數。

以簡單的計算機服務為例。 此服務會採用兩個數字並傳回其總和。 這是一種沒有與其相關聯資料的純粹無狀態服務。

現在，假設另有一個計算機服務，除了計算加總之外，也有方法可以傳回上一個計算總和。 此服務現在已可設定狀態，其中有一些狀態是要寫入 (當計算新的加總時) 及讀取 (當傳回上一個計算加總時) 的狀態。

在 Azure Service Fabric 中，第一個服務稱為無狀態服務。 第二個服務稱為可設定狀態的服務。

## <a name="storing-service-state"></a>儲存服務狀態
狀態可進行外部化，也可並存在運作該狀態的程式碼中。 狀態的外部化通常是使用外部資料庫或存放區來完成。 在計算機範例中，這可能是 SQL 資料庫，亦即目前儲存在資料表中的結果。 每個計算總和的要求會在此資料列上執行更新。

狀態也可以使用處理此程式碼的程式碼進行共置。 Service Fabric 中的可設定狀態服務是使用此模型來建置。 Service Fabric 提供的基礎結構可確保在發生失敗時，此狀態仍具高可用性並會容錯。

## <a name="next-steps"></a>後續步驟
如需 Service Fabric 概念的詳細資訊，請參閱：

* [Service Fabric 服務的可用性](service-fabric-availability-services.md)
* [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)
* [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)




<!--HONumber=Nov16_HO3-->


