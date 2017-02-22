---
title: "Service Fabric 服務的延展性 | Microsoft Docs"
description: "描述如何調整 Service Fabric 服務"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>調整 Service Fabric 應用程式
Azure Service Fabric 可管理叢集中所有節點上的服務、資料分割和複本，讓您輕鬆建置可調整的應用程式。 這可最大化資源使用率。

Service Fabric 應用程式的高延展性可以透過兩種方式來達成：

1. 在服務資料分割層級上調整
2. 在具名服務執行個體層級上調整

## <a name="scaling-at-the-partition-level"></a>調整資料分割層級
Service Fabric 支援資料分割。 資料分割可將個別服務分割成多個獨立的資料分割，每個資料分割各有服務整體狀態的某些部分。 [資料分割概觀](service-fabric-concepts-partitioning.md) 提供所支援資料分割配置類型的相關資訊。 每個資料分割的複本會散佈到叢集中的節點。 假設有一個使用範圍資料分割配置的服務，其中低索引鍵為 0、高索引鍵為 99、資料分割計數為 4。 在三個節點叢集中，服務可能使用四個複本進行配置，並共用每個節點上的資源，如下所示：

<center>
![使用三個節點分割配置](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

如果您增加節點數目，Service Fabric 會利用新節點上的資源，並將一些現有的複本移至那裡。 將節點數目增加至四個，服務現在有三個複本 (各屬於不同的資料分割) 在每個節點上執行，資源使用率和效能更高。

<center>
![使用四個節點分割配置](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>調整服務名稱層級
服務執行個體是應用程式名稱和服務類型名稱的特定執行個體 (請參閱 [Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md))。 在服務建立期間，您可以指定要使用的資料分割配置 (請參閱 [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md))。

第一層的調整比例是根據服務名稱。 當較舊的服務執行個體變得忙碌時，您可以建立服務的執行個體 (可選擇使用不同的資料分割層級)。 這可讓新的服務取用者使用較不忙碌的服務執行個體，而非較忙碌的服務執行個體。

增加容量的一種做法是使用新的資料分割配置建立新的服務執行個體。 但這樣比較複雜。 因為任何取用的用戶端需要了解何時及如何使用不同名稱的服務。 還有另一種做法，就是由管理或中繼服務決定哪個服務和資料分割應該處理每個要求。

### <a name="example-scenario-embedded-dates"></a>範例案例：內嵌的日期
一個可能的案例是使用日期資訊作為服務名稱的一部分。 例如，您可以針對 2013 年加入的所有客戶使用具有特定名稱的服務執行個體，而 2014 年加入的客戶則使用其他名稱。 此命名配置可以允許名稱根據日期 (2014年的服務執行個體可以透過隨選方式建立來作為 2014 年的方法) 而以程式設計方式增加。

不過，此方法是根據使用應用程式特定命名資訊的用戶端，超出 Service Fabric 的知識領域之外。

* *使用命名慣例*：當您在 2013 年啟動應用程式時，將建立一個稱為 fabric:/app/service2013 的服務。 接近到 2013 年的第二季時，則將建立另一個稱為 fabric:/app/service2014 的服務。 這兩個服務都屬於相同的服務類型。 在此方法中，您的用戶端必須根據年份運用邏輯來建構適當的服務名稱。
* *使用查閱服務*：另一個模式是提供次要的「查閱服務」，可提供所需索引鍵的服務名稱。 接著新的服務執行個體可透過查閱服務來建立。 查閱服務本身不會保留任何應用程式資料，僅保留其所建立的服務名稱相關資料。 因此，在以上的年份範例中，用戶端會先連絡查閱服務，找出某一年負責處理資料的服務名稱。 然後，用戶端就使用該服務名稱來執行實際作業。 第一次的查閱結果可以進行快取。

## <a name="putting-it-all-together"></a>總整理
讓我們根據這裡討論的所有概念來探討另一種情況。

假設︰您想要建立一個服務做為通訊錄，以儲存名稱和連絡人資訊。 您會有多少使用者？ 每個使用者會儲存多少連絡人？ 在第一次建立服務時，很難完全釐清這些資料。 挑選錯誤的資料分割計數會導致以後調整時發生問題。 到底為什麼要嘗試為所有使用者挑選單一資料分割配置？

在這種情況下，請考慮改用下列模式︰
1. 建置「服務管理員」，而不要嘗試事先為每個人挑選資料分割配置。
2. 當客戶註冊您的服務時，管理員服務就負責查看他們的資訊。 然後，根據這項資訊而定，「只針對該客戶」建立「實際」連絡人儲存體服務的執行個體。 這種動態建立服務的模式有許多好處︰

    * 您不必事先為所有使用者猜測正確的資料分割計數
    * 資料分割，因為每個客戶都有自己的服務複本
    * 每個客戶服務可以有不同的設定方式，根據預期的級別，依需要使用較多或較少的資料分割或複本。
      * 例如，假設客戶付費使用「黃金」層次 - 他們可以取得更多複本或更高的資料分割計數
      * 或者，假設他們提供資訊指出所需的連絡人數目「很少」- 他們只會取得少數資料分割。
    * 在等待客戶出現時，您沒有執行一堆服務執行個體或複本
    * 一旦客戶離開，就由管理員刪除它所建立的服務，即可從您的服務中移除客戶的資訊，如此簡單。

## <a name="next-steps"></a>後續步驟
如需 Service Fabric 概念的詳細資訊，請參閱下列文章：

* [Service Fabric 服務的可用性](service-fabric-availability-services.md)
* [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)
* [定義和管理狀態](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


