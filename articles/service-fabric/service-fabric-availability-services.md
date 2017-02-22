---
title: "Service Fabric 服務的可用性 | Microsoft Docs"
description: "描述錯誤偵測、容錯移轉與服務復原"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 1db7b4bcfa4b7c474a4b0eb4ef469a6cb1fe54a0


---
# <a name="availability-of-service-fabric-services"></a>Service Fabric 服務的可用性
本文章概述 Service Fabric 如何維護服務的可用性。

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric 無狀態服務的可用性
Azure Service Fabric 服務可能是具狀態或無狀態。 無狀態服務是一種應用程式服務，並不具有任何 [本機永續性狀態](service-fabric-concepts-state.md)。

建立無狀態服務需要定義執行個體計數。 執行個體計數定義了應該在叢集中執行之無狀態服務應用程式邏輯的執行個體數目。 增加執行個體數目是一種相應放大無狀態服務規模的建議方式。

當在無狀態服務的任何執行個體上偵測到錯誤時，系統就會在叢集中的某個合格節點上建立新的執行個體。

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric 可設定狀態服務的可用性
具狀態服務具有某些與其相關聯的狀態。 在 Service Fabric 中，可設定狀態服務會模型化為複本集。 每個複本是一種服務程式碼的執行個體，其中具有一個狀態複本。 讀取與寫入作業會在單一複本上執行 (稱為「主要複本」)。 從寫入作業對狀態進行的變更會「複寫」到多個其他複本 (稱為「作用中次要複本」)。 「主要複本」和「作用中次要複本」的組合即構成服務的複本集。

只能有一個「主要複本」為讀取和寫入要求提供服務，但可以有多個「作用中次要複本」。 「作用中次要複本」的數目是可設定的，且複本數目越高，所能容許的並行軟體和硬體失敗次數就越多。

如果「主要複本」當機，Service Fabric 就會讓其中一個「作用中次要複本」成為新的「主要複本」。 此「作用中次要複本」已經有更新過的狀態版本 (透過「複寫」)，因此能夠繼續處理進一步的讀取和寫入作業。

此概念 (即複本不是「主要複本」就是「作用中次要複本」) 稱為「複本角色」。

### <a name="replica-roles"></a>複本角色
複本角色用來管理由該複本管理的狀態生命週期。 扮演「主要」角色的複本會為讀取要求提供服務。 「主要複本」同時也會藉由更新寫入要求的狀態並複寫變更，來處理所有寫入要求。 這些變更會套用至複本集內的「作用中次要複本」。 「作用中次要複本」的工作是接收「主要複本」已複寫的狀態變更，並更新其狀態檢視。

> [!NOTE]
> 更高階的程式設計模型 (例如 [Reliable Actors 架構](service-fabric-reliable-actors-introduction.md)和 [Reliable Services](service-fabric-reliable-services-introduction.md)) 會從開發人員抽離複本角色的概念。 在動作項目中，角色的概念是不必要的，但在服務中則是必要時可以運用，不過大部分會予以簡化。
>
>

## <a name="next-steps"></a>後續步驟
如需有關 Service Fabric 概念的詳細資訊，請參閱下列文章：

* [Service Fabric 服務的延展性](service-fabric-concepts-scalability.md)
* [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)
* [定義和管理狀態](service-fabric-concepts-state.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO1-->


