---
title: "另行快取模式 | Azure | Microsoft Docs"
description: "依需要從資料存放區將資料載入快取中"
categories:
- data-management
- performance-scalability
keywords: "設計模式"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 20329791f93e7339a00ff44d5e1bf34492b6587b

---

# <a name="cache-aside"></a>另行快取

依需要從資料存放區將資料載入快取中。 這可以改善效能並且有助於維持快取中所保留資料與基礎資料存放區中資料之間的一致性。

## <a name="context-and-problem"></a>內容和問題

應用程式使用快取以減少重複存取資料存放區中存放的資訊。 但是，我們不能期望快取的資料會永遠與資料存放區中的資料完全一致。 應用程式應該實作的策略是，協助確保快取中的資料盡可能保持最新，也可以偵測並處理當快取中的資料變成過時的情況。

## <a name="solution"></a>方案

許多商業的快取系統提供貫穿式讀取和貫穿式寫入/事後寫入作業。 在這些系統中，應用程式會參考快取來擷取資料。 如果資料不在快取中，就會從資料存放區擷取，並加入快取。 快取中保留的資料若有任何修改，也會自動寫回資料存放區。

如果快取不提供這項功能，使用快取的應用程式就必須負責維護資料。

應用程式可以實作另行快取策略，模擬貫穿式讀取快取的功能。 此策略會依需要將資料載入快取。 此圖說明使用另行快取模式在快取中儲存資料。

![使用另行快取模式在快取中儲存資料](images/cache-aside-diagram.png)


如果應用程式更新資訊，可以對資料存放區進行修改，並且讓快取中對應的項目失效，來遵循貫穿式寫入策略。

而當之後需要此項目時，使用另行快取策略將從資料存放區擷取更新過的資料，再加回快取。

## <a name="issues-and-considerations"></a>問題和考量

當您決定如何實作此模式時，請考慮下列幾點： 

**快取資料的存留期**。 許多快取實作的到期原則是，如果資料在指定的期間內沒有被存取過，就會讓資料無效並從快取移除。 如果要讓另行快取有效，請確定到期原則符合使用該資料的應用程式的存取模式。 不要讓到期期間太短，因為這會造成應用程式要持續從資料存放區擷取資料並加入快取。 同樣地，不要讓到期期間太長，而讓快取的資料有可能變成過時。 請記住，快取對於相對靜態的資料或經常讀取的資料是最有效的。

**收回資料**。 大部分的快取相較於資料來源的資料存放區都有大小限制，因此必要時會收回資料。 大部分的快取會採用最近最少使用過的原則來選取要收回的項目，但這可以自訂。 設定快取的全域到期屬性和其他屬性，以及每個快取項目的到期屬性，可確保快取符合成本效益。 對快取中的每個項目套用全域收回原則不一定永遠適合。 例如，如果從資料存放區擷取快取的項目所耗費的資源很高，在快取中保留此項目可能會比經常存取成本較低的項目有利。

**預備快取**。 許多解決方案會使用應用程式可能需要啟動處理程序的資料預先填入快取。 如果有些資料到期或被收回，另行快取模式就還是很有用。

**一致性**。 實作另行快取模式並不保證資料存放區與快取之間的一致性。 資料存放區中的項目可能會隨時由外部處理程序變更，而這項變更可能要到下次載入項目時才會反映在快取中。 在資料存放區之間會複寫資料的系統中，如果經常發生同步處理，這個問題可能會變得很嚴重。

**本機 (記憶體內) 快取**。 快取對於應用程式執行個體可以是本機的，並儲存在記憶體中。 如果應用程式會重複存取相同的資料，另行快取在這樣的環境中就相當有用。 不過，本機快取屬私人性質，因此不同的應用程式執行個體對於相同的快取資料會分別建立一份複本。 這項資料在快取之間可能很快就會變得不一致，因此可能需要更頻繁地讓私人快取中保留的資料到期，然後重新整理資料。 在這些情況下，請考慮使用共用或分散式快取機制。

## <a name="when-to-use-this-pattern"></a>使用此模式的時機

使用此模式的時機包括：

- 快取並不提供原生的貫穿式讀取和貫穿式寫入作業。
- 資源需求無法預測。 此模式可讓應用程式依需要載入資料。 它不會假設應用程式將會事先需要哪些資料。

此模式可能不適合下列時機︰

- 當快取的資料集是靜態的。 如果資料可放入可用的快取空間，在啟動時使用資料預先填入快取，並套用讓資料不會過期的原則。
- 在裝載於 Web 伺服陣列中的 Web 應用程式中快取工作階段狀態資訊。 在此環境中，您應該避免引入以用戶端-伺服器同質性為基礎的相依性。

## <a name="example"></a>範例

在 Microsoft Azure 中，您可以使用 Azure 快取建立可由多個應用程式執行個體共用的分散式快取。 下列程式碼範例中的 `GetMyEntityAsync` 方法示範如何實作以 Azure 快取為基礎的另行快取模式。 此方法使用貫穿式讀取方法從快取中擷取物件。

物件的識別方式是使用整數識別碼做為索引鍵。 `GetMyEntityAsync` 方法會根據此索引鍵產生字串值 (Azure 快取 API 使用字串做為索引鍵值)，並嘗試從快取擷取有此索引鍵的項目。 如果找到相符的項目，就會傳回。 如果快取中沒有符合的項目，`GetMyEntityAsync` 方法就會從資料存放區擷取物件、將它加入快取，然後將它傳回。 實際從資料存放區擷取資料的程式碼已省略，因為它與資料存放區有關。 請注意，快取的項目已設定為過期，以避免在其他地方經過更新而變成過時。

```csharp
private DataCache cache;

public async Task<MyEntity> GetMyEntityAsync(int id)
{  
  // Define a unique key for this method and its parameters.
  var key = string.Format("StoreWithCache_GetAsync_{0}", id);
  var expiration = TimeSpan.FromMinutes(3);
  bool cacheException = false;

  try
  {
    // Try to get the entity from the cache.
    var cacheItem = cache.GetCacheItem(key);
    if (cacheItem != null)
    {
      return cacheItem.Value as MyEntity;
    }
  }
  catch (DataCacheException)
  {
    // If there's a cache related issue, raise an exception 
    // and avoid using the cache for the rest of the call.
    cacheException = true;
  }

  // If there's a cache miss, get the entity from the original store and cache it.
  // Code has been omitted because it's data store dependent.  
  var entity = ...;

  if (!cacheException)
  {
    try
    {
      // Avoid caching a null value.
      if (entity != null)
      {
        // Put the item in the cache with a custom expiration time that 
        // depends on how critical it is to have stale data.
        cache.Put(key, entity, timeout: expiration);
      }
    }
    catch (DataCacheException)
    {
      // If there's a cache related issue, ignore it
      // and just return the entity.
    }
  }

  return entity;
}
```

>  範例使用 Azure 快取 API 來存取存放區，並從快取中擷取資訊。 如需詳細資訊，請參閱[使用 Microsoft Azure 快取](https://msdn.microsoft.com/library/azure/hh914165.aspx)。

下面顯示的 `UpdateEntityAsync` 方法示範如何讓快取中的物件在值被應用程式變更時變成無效。 這是貫穿式寫入方法的範例。 程式碼會更新原始的資料存放區，然後呼叫 `Remove` 方法並指定索引鍵 (程式碼已省略，因為它與資料存放區有關)，從快取中移除快取的項目。

>  步驟的順序很重要。 如果項目在快取更新之前就移除，用戶端應用程式在資料存放區中的項目變更之前有一小段時間擷取資料 (因為在快取中找不到資料)，導致快取包含過時的資料。

```csharp
public async Task UpdateEntityAsync(MyEntity entity)
{
  // Update the object in the original data store
  await this.store.UpdateEntityAsync(entity).ConfigureAwait(false);

  // Get the correct key for the cached object.
  var key = this.GetAsyncCacheKey(entity.Id);

  // Then, invalidate the current cache object
  this.cache.Remove(key);
}

private string GetAsyncCacheKey(int objectId)
{
  return string.Format("StoreWithCache_GetAsync_{0}", objectId);
}
```

## <a name="related-guidance"></a>相關的指引 

以下是實作此模式的相關資訊︰

- [快取指引](https://msdn.microsoft.com/library/dn589802.aspx)。 提供如何在雲端解決方案中快取資料，以及當您實作快取時應該考慮的問題的其他資訊。

- [資料一致性入門](https://msdn.microsoft.com/library/dn589800.aspx)。 雲端應用程式通常使用分散在資料存放區各處的資料。 在此環境中管理和維護資料的一致性，是系統一個相當關鍵的部分，尤其是可能發生並行存取和可用性的問題。 此入門說明有關分散式資料之間一致性的問題，並摘要說明應用程式如何實作最終一致性，以維持資料的可用性。



<!--HONumber=Nov16_HO3-->


