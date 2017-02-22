---
title: "Azure Service Fabric Reliable Services 生命週期的概觀 | Microsoft Docs"
description: "了解 Service Fabric Reliable Services中不同的生命週期事件"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: 1472bd1647b620d1f0489cdabbed58e72585aef7
ms.openlocfilehash: ad1f713f2b12af4f778b9b14edf33a568d2aa8b1

---

# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 生命週期概觀
在考慮 Reliable Services 的生命週期時，生命週期的基礎最重要。 一般而言：

* 在啟動期間
  * 建構服務
  * 它們有機會建構和傳回零個以上的接聽程式
  * 開啟任何傳回的接聽程式，能夠與服務進行通訊
  * 呼叫服務的 RunAsync 方法，可讓服務進行長時間執行的工作或背景工作
* 在關閉期間
  * 取消傳遞給 RunAsync 的取消權杖，並關閉接聽程式
  * 完成後，就解構服務物件本身

這些事件的確切順序還有一些細節可討論。 特別的是，根據 Reliable Service 是無狀態或具狀態而定，事件的順序可能稍有變化。 此外，對於具狀態服務，我們必須處理「主要」複本互換情況。 在此序列期間，「主要」角色會轉移至另一個複本 (或回來)，但服務不會關閉。 最後，我們必須考慮錯誤或失敗狀況。

## <a name="stateless-service-startup"></a>無狀態服務啟動
無狀態服務的生命週期相當簡單。 以下是事件的順序︰

1. 建構服務
2. 接著，兩件事平行發生︰
    - 叫用 `StatelessService.CreateServiceInstanceListeners()`，並「開啟」任何傳回的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.OpenAsync()`)
    - 呼叫服務的 RunAsync 方法 (`StatelessService.RunAsync()`)
3. 呼叫服務本身的 OnOpenAsync 方法 (如果有的話) (特別是呼叫 `StatelessService.OnOpenAsync()`。 這是不常用的覆寫，但可用)。

請務必留意，建立及開啟接聽程式和 RunAsync 時，沒有一定的呼叫順序。 接聽程式可能在 RunAsync 啟動之前開啟。 同樣地，在通訊接聽程式開啟或建構之前，可能就已叫用 RunAsync。 如果需要任何同步處理，則留給實作者去負責。 常見的解決方案︰

* 有時，必須等到建立其他一些資訊或完成工作後，接聽程式才能運作。 針對無狀態服務，通常可以在服務的建構函式中、`CreateServiceInstanceListeners()` 呼叫期間，或接聽程式本身的建構過程中完成工作。
* 有時，RunAsync 中的程式碼要等到接聽程式開啟之後才會啟動。 在此情況下，額外的協調有必要。 一個常用的解決方案是在接聽程式內設置旗標，以指接聽程式出何時完成，在實際工作繼續之前，RunAsync 中會先檢查此旗標。

## <a name="stateless-service-shutdown"></a>無狀態服務關閉
關閉無狀態服務時，也依循相同的模式，只是順序相反：

1. 平行
    - 「關閉」任何開啟的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.CloseAsync()`)
    - 取消傳遞至 `RunAsync()` 的取消權杖 (檢查取消權杖的 `IsCancellationRequested` 屬性傳回 true，還有呼叫權杖的 `ThrowIfCancellationRequested` 方法是否傳回 `OperationCanceledException`)
2. 當每個接聽程式上完成 `CloseAsync()`，且 `RunAsync()` 也完成時，就呼叫服務的 `StatelessService.OnCloseAsync()` 方法 (如果有的話) (這也是不常用的覆寫)。
3. `StatelessService.OnCloseAsync()` 完成之後，就解構服務物件

## <a name="stateful-service-startup"></a>具狀態服務啟動
具狀態服務的模式類似於無狀態服務，只有一些不同。 啟動具狀態服務時，事件的順序如下︰

1. 建構服務
2. 呼叫 `StatefulServiceBase.OnOpenAsync()`。 (這是服務中不常用的覆寫)。
3. 如果涉及的服務複本是「主要」，則會平行發生下列事件，否則服務會跳至步驟 4
    - 叫用 `StatefulServiceBase.CreateServiceReplicaListeners()`，並「開啟」任何傳回的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.OpenAsync()`)
    - 呼叫服務的 RunAsync 方法 (`StatefulServiceBase.RunAsync()`)
4. 當複本接聽程式的所有 `OpenAsync()` 呼叫完成，且 `RunAsync()` 也已啟動時 (或因為此複本目前是次要，而略過這些步驟)，就呼叫 `StatefulServiceBase.OnChangeRoleAsync()`。 (這是服務中不常用的覆寫)。

類似於無狀態服務，在建立和開啟接聽程式及呼叫 RunAsync 時，不會協調先後順序。 解決方案也大致相同，但有一個額外情況︰假設抵達通訊接聽程式的呼叫需要[可靠的集合](service-fabric-reliable-services-reliable-collections.md)內保留的資訊才能運作。 因為在還無法讀取或寫入可靠的集合，且 RunAsync 也還無法啟動之前，通訊接聽程式可能先開啟，因此，一些額外的協調有必要。 最簡單且最常見的解決方法是由通訊接聽程式傳回某個錯誤碼，讓用戶端知道要重試要求。

## <a name="stateful-service-shutdown"></a>具狀態服務關閉
類似於無狀態服務，關閉期間的生命週期事件與啟動期間相同，但順序相反。 具狀態服務關閉時會發生下列事件︰

1. 平行
    - 「關閉」任何開啟的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.CloseAsync()`)
    - 取消傳遞至 `RunAsync()` 的取消權杖 (檢查取消權杖的 `IsCancellationRequested` 屬性傳回 true，還有呼叫權杖的 `ThrowIfCancellationRequested` 方法是否傳回 `OperationCanceledException`)
2. 當每個接聽程式上完成 `CloseAsync()`，且 `RunAsync()` 也完成時 (這只有在此服務複本是「主要」時才需要)，就呼叫服務的 `StatefulServiceBase.OnChangeRoleAsync()`。 (這是服務中不常用的覆寫)。
3. 當 `StatefulServiceBase.OnChangeRoleAsync()` 方法完成時，就呼叫 `StatefulServiceBase.OnCloseAsync()`方法 (這也是不常用的覆寫，但可用)。
3. `StatefulServiceBase.OnCloseAsync()` 完成之後，就解構服務物件。

## <a name="stateful-service-primary-swaps"></a>具狀態服務主要複本互換
當具狀態服務執行時，只有此具狀態服務的「主要」複本會開啟通訊接聽程式和呼叫 RunAsync 方法。 建構次要，但看不到進一步的呼叫。 不過，當具狀態服務執行時，哪個複本目前是「主要」可能會改變。 就複本可見的生命週期事件來說，這代表什麼？ 具狀態複本所看到的行為，取決於它在互換期間是降級或升級的複本。

### <a name="for-the-primary-being-demoted"></a>降級的主要複本
Service Fabric 需要此複本停止處理訊息並結束它正在進行的任何背景工作。 因此，這個步驟類似於服務關閉時的情形。 差別在於服務仍為「次要」，並不會解構或關閉。 呼叫下列 API：

1. 平行
    - 「關閉」任何開啟的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.CloseAsync()`)
    - 取消傳遞至 `RunAsync()` 的取消權杖 (檢查取消權杖的 `IsCancellationRequested` 屬性傳回 true，還有呼叫權杖的 `ThrowIfCancellationRequested` 方法是否傳回 `OperationCanceledException`)
2. 當每個接聽程式上完成 `CloseAsync()`，且 `RunAsync()` 也完成時，就呼叫服務的 `StatefulServiceBase.OnChangeRoleAsync()`。 (這是服務中不常用的覆寫)。

### <a name="for-the-secondary-being-promoted"></a>升級的次要複本
同樣地，Service Fabric 需要此複本開始接聽網路上的訊息 (如果它這麼做) 並啟動它關注的任何背景工作。 因此，這個程序類似於建立服務時的情形，差別在於複本它本身已存在。 呼叫下列 API：

1. 平行
    - 叫用 `StatefulServiceBase.CreateServiceReplicaListeners()`，並「開啟」任何傳回的接聽程式 (在每個接聽程式上呼叫 `ICommunicationListener.OpenAsync()`)
    - 呼叫服務的 RunAsync 方法 (`StatefulServiceBase.RunAsync()`)
4. 當複本接聽程式的所有 `OpenAsync()` 呼叫完成，且 `RunAsync()` 也已啟動時 (或因為這是次要複本，而略過這些步驟)，就呼叫 `StatefulServiceBase.OnChangeRoleAsync()`。 (這是服務中不常用的覆寫)。

## <a name="notes-on-service-lifecycle"></a>服務生命週期的注意事項
* `RunAsync()` 方法和 `CreateServiceReplicaListeners/CreateServiceInstanceListeners` 呼叫都是選擇性。 一個服務可能具有其中一個、兩者或都沒有。 例如，若服務本身自行負責回應使用者呼叫，則不需要實作 `RunAsync()`。 只需要通訊接聽程式及其相關聯的程式碼。 同樣地，建立和傳回通訊接聽程式是選擇性，因為服務可能只有背景工作要處理，所以只需要實作 `RunAsync()`
* 服務可以成功完成 `RunAsync()` 並返回。 這不算是失敗狀況，只是表示服務的背景工作完成。 對於具狀態 Reliable Services，如果服務從主要降級，然後又升級回到主要，則會再次呼叫 `RunAsync()`。
* 如果服務擲回某些非預期的例外狀況而離開 `RunAsync()`，則算是失敗，將會關閉服務物件並回報健全狀況錯誤。
* 雖然從這些方法傳回沒有時間限制，但您會立即喪失寫入到可靠的集合的能力，並因此無法完成任何實際工作。 建議您盡快在收到取消要求後傳回。 如果您的服務未於合理的時間內回應這些 API 呼叫，Service Fabric 可能會強制終止服務。 這通常只發生在應用程式升級期間或刪除服務時。 此逾時預設為 15 分鐘。
* 對於具狀態服務，ServiceReplicaListeners 還有另一個選項可讓它們在次要複本上啟動。 這不常用，但生命週期中唯一的改變是即使複本為「次要」，仍會呼叫 `CreateServiceReplicaListeners()` (並「開啟」產生的接聽程式)。 同樣地，如果複本後來轉換為主要，在變更為「主要」的過程中，將會關閉、解構接聽程式，並建立和「開啟」新的接聽程式。
* `OnCloseAsync()` 路徑失敗會導致呼叫 `OnAbort()`，這是服務清除並釋放已宣告之任何資源的最後努力機會。

## <a name="next-steps"></a>後續步驟
* [Reliable Services 簡介](service-fabric-reliable-services-introduction.md)
* [Reliable Services 快速入門](service-fabric-reliable-services-quick-start.md)
* [Reliable Services 的進階用法](service-fabric-reliable-services-advanced-usage.md)



<!--HONumber=Jan17_HO1-->


