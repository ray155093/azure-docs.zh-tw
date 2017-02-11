---
title: "Service Fabric 叢集 Resource Manager - 同質性 | Microsoft Docs"
description: "Service Fabric 服務的設定同質性概觀"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>在 Service Fabric 中設定並使用服務同質性
同質性是一個控制項，主要提供來協助簡化將較大型的單體式應用程式轉換到雲端和微服務世界的程序。 也就是說，在某些情況下它也可當做提升服務效能的合法最佳化，雖然這可能會有副作用。

假設您正在將較大型 app，或只是設計時未將微服務納入考量的 app 引進到 Service Fabric。 這種轉換很常見。 首先要將整個應用程式提升至環境中，封裝應用程式，並確定可以順暢執行。 接著，開始將其分解成不同的較小服務，使其可彼此通訊。

然後就是「糟糕」了。 「糟糕」通常屬於下列其中一個類別︰

1. 單體式應用程式中的某個元件 X 對元件 Y 具有不明的相依性，但您將這些元件變成個別的服務。 因為現在這些服務是在叢集中不同的節點上執行，它們已中斷。
2. 這些項目會透過 (本機具名管道 | 共用記憶體 | 磁碟上的檔案) 來通訊，但基於效能理由，真的需要立即寫入至共用資源。 稍後會移除該硬式相依性。
3. 一切都沒問題，但事實上，這兩個元件具有很多對話/效能敏感的內容。 當我將它們移到個別服務時，整體應用程式效能會遭到重挫或導致延遲增加。 如此一來，整體應用程式就不會符合預期。

在這些情況下，我們不想要失去重構工作，也不想要回到單體式。 不過，在我們能夠將元件重新設計的像服務一樣自然運作之前 (或能夠以其他某些方式解決效能期望之前)，我們需要有位置方面的概念。

怎麼辦？ 嗯，您可以嘗試開啟同質性。

## <a name="how-to-configure-affinity"></a>如何設定同質性
若要設定同質性，您可以定義兩個不同服務之間的同質關聯性。 您可以將此同質性想像成在一個服務上「指向」另一個服務，並假設「此服務只有在該服務執行所在的地方才能執行」。 有時我們會將這些同質性稱為父子式關聯性 (您會在父系上指向子系)。 同質性可確保某一個服務的複本或執行個體是放置在與另一個服務的複本或執行個體相同的節點上。

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>各種同質性選項
同質性是透過數種相互關聯的結構描述之一來表示，而且有兩種不同的模式。 同質性的最常見模式是我們稱為 NonAlignedAffinity 的模式。 在 NonAlignedAffinity 中，不同服務的複本或執行個體會放在同一個節點上。 另一個模式是 AlignedAffinity。 對齊的同質性只有在與具狀態服務搭配使用時才有用。 設定兩個具狀態服務來具備對齊的同質性，可以確保那些服務的主要複本都位於和彼此相同的節點上。 它也會讓那些服務的每一對次要複本位於相同的節點上。 它也可能 (但較不常見) 針對具狀態服務設定 NonAlignedAffinity。 對於 NonAlignedAffinity，會將兩個具狀態服務的不同複本收集在相同節點上，但不會嘗試對齊它們的主要複本或次要複本。

<center>
![同質性模式及其效果][Image1]
</center>

### <a name="best-effort-desired-state"></a>盡力而為的期望狀態
在同質性與單體式架構之間有一些差異。 產生這些差異的原因有許多是因為同質關聯性是以盡力而為的方式取得。 具有同質關聯性的服務是本質上不同的實體，可能會失敗以及被個別移動。 也有一些原因可能會中斷同質關聯性。 例如，同質關聯性中只有部分服務物件的容量限制可以適用於指定節點。 在這些情況下，即使有同質關聯性，也會因為其他條件約束而無法強制執行。 如果可能這樣做的話，稍後會自動更正違規。

### <a name="chains-vs-stars"></a>鏈結與星形的比較
目前，叢集資源管理員無法模擬同質關聯性的鏈結。 這表示是，如果有一個服務是某一個同質關聯性中的子系，則該服務不能是另一個同質關聯性中的父系。 如果您想要建立此類型的關聯性模型，您實際上必須將它建立為星形模型，而不是鏈結模型。 為了從鏈結轉變為星形，最下面的子系會變成第一個子系的父系。 根據服務的排列方式而定，您可能必須這樣做許多次。 如果沒有自然父系服務，您可能必須建立一個作為預留位置。 根據您的需求而定，您也可能考慮[應用程式群組](service-fabric-cluster-resource-manager-application-groups.md)。

<center>
![同質關聯性內容中的鏈結與星形的比較][Image2]
</center>

目前關於同質關聯性的另一個要注意事項是它們是雙向的。 這表示「同質性」規則只會強制規定子系就是父系所在的位置，而不是父系與子系位於同一處。 例如，假設父系突然容錯移轉至另一個節點。 發生這種情況時，叢集資源管理員會認為一切都沒問題，直到發現子系與父代沒有在同一處。 由於這些是具有不同生命週期的不同服務，同質關聯性不可能完美或立即強制執行。

### <a name="partitioning-support"></a>支援分割
請注意，關於同質性要注意的最後一點是，分割父系的地方不支援同質關聯性。 我們最終可能會支援這一點，但目前並不允許。

## <a name="next-steps"></a>後續步驟
* 如需可用來設定服務的其他選項的詳細資訊，請參閱有關其他可用之叢集資源管理員設定的主題：[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)
* 在做法上，例如將服務限制在一小群電腦上，並嘗試彙總一組服務的負載，請使用[應用程式群組](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Feb17_HO2-->


