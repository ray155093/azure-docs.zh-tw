<properties
   pageTitle="Reliable Actors 狀態管理 | Microsoft Azure"
   description="說明如何管理、保存及且複寫 Reliable Actors 狀態以提供高可用性。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Reliable Actors 狀態管理

Reliable Actors 是可封裝邏輯和狀態的單一執行緒物件。由於動作項目會在 Reliable Services 上執行，因此，它們可以利用 Reliable Services 所使用的相同持續性和複寫機制，以可靠的方式維護狀態。如此一來，動作項目就不會在失敗之後、在記憶體回收之後重新啟動，或者因為資源平衡和升級的緣故而在叢集中的節點之間移動時，遺失它們的狀態。

## 狀態持續性和複寫

所有的 Reliable Actors 會被視為「具狀態」，因為每個動作項目執行個體都會對應到唯一的識別碼。這表示，對同一個動作項目識別碼所進行的重複呼叫將會路由傳送到同一個動作項目執行個體。這是相較於無狀態系統的情況，無狀態系統中的用戶端呼叫不一定每次都會路由傳送到同一部伺服器。基於這個理由，動作項目服務永遠都是具狀態服務。

不過，即使動作項目會被視為具狀態，但並不表示它們必須以可靠的方式儲存狀態。動作項目可以根據其資料儲存體需求來選擇狀態持續性和複寫的層級︰

 - **保存的狀態︰**狀態會保存於磁碟，並複寫至 3 個以上的複本。這是最持久的狀態儲存選項，可透過完整的叢集中斷來保存狀態。
 - **變動性狀態︰**狀態會複寫至 3 個以上的複本，而且只會保存於記憶體中。這可針對節點失敗、動作項目失敗，以及在升級和資源平衡期間提供恢復能力。不過，狀態不會保存到磁碟，因此，如果同時遺失所有複本，狀態也會遺失。
 - **沒有保存的狀態︰**狀態不會複寫，也不會寫入磁碟。適用於完全不需要以可靠方式維護狀態的動作項目。
 
每個層級的持續性只是您服務的不同「狀態供應器」和「複寫」組態。是否要將狀態寫入磁碟取決於「狀態供應器」(Reliable Service 中儲存狀態的元件)，而複寫取決於要使用多少個複本來部署服務。就如同 Reliable Services，您可以輕鬆地手動設定狀態供應器和複本計數。動作項目架構提供屬性，在動作項目上使用時，將自動選取預設的狀態供應器，並自動產生複本計數的設定，以達到這三個持續性設定的其中一個。

### 保存的狀態
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
此設定會使用狀態供應器，在磁碟上儲存資料，並自動將服務複本計數設定為 3。

### 變動性狀態
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
此設定會使用僅在記憶體中的狀態供應器，並將複本計數設定為 3。

### 沒有保存的狀態

```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
此設定會使用僅在記憶體中的狀態供應器，並將複本計數設定為 1。

### 預設值和產生的設定

使用 `StatePersistence` 屬性時，在動作項目服務啟動時，會在執行階段自動為您選取狀態供應器。不過，複本計數是在編譯時期由 Visual Studio 動作項目建置工具所設定。建置工具會在 ApplicationManifest.xml 中自動為動作項目服務產生「預設服務」。參數是針對「複本集大小下限」和「目標複本集大小」來建立。您當然可以手動變更這些參數，不過，每當 `StatePersistence` 屬性變更時，參數將會設定為所選 `StatePersistence` 屬性的預設複本集大小值，覆寫所有舊值。換句話說，您在 ServiceManifest.xml 中設定的值將**只**會在當您變更 `StatePersistence` 屬性值時，於建置階段覆寫。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## 狀態管理員

每個動作項目執行個體都有它自己的狀態管理員︰以可靠方式儲存金鑰-值組的字典式資料結構。狀態管理員是包住狀態供應器的包裝函式。它可以用來儲存資料，而不論使用的是哪一個持續性設定，但不保證執行中的動作項目服務可以透過輪流升級從變動性 (僅在記憶體中) 狀態設定變更保存的狀態設定，同時保留資料。但是，針對執行中的服務變更複本計數是可行的。

狀態管理員索引鍵必須是字串，而值是泛型且可以是任何類型，包括自訂類型。儲存在狀態管理員中的值必須是可進行資料合約序列化的，因為根據動作項目的狀態持續性設定，它們可能會在複寫期間透過網路傳輸至其他節點，而且可能會寫入磁碟。

狀態管理員會公開一般字典方法來管理狀態，類似於在可靠的字典中找到的項目。

### 存取狀態

狀態可以透過狀態管理員依索引鍵來存取。狀態管理員方法全都是非同步的，因為當動作項目具有保存的狀態時，它們可能需要磁碟 I/O。第一次存取時，會將狀態物件快取於記憶體中。重複存取作業會從記憶體中直接存取物件並以同步方式傳回，而不會造成磁碟 I/O 或非同步內容切換的負擔。狀態物件會在下列情況中從快取移除︰

 - 從狀態管理員中擷取物件之後，動作項目方法會擲回未處理的例外狀況。
 - 動作項目會在已停用之後或因為失敗而重新啟動。
 - 如果狀態供應器會將狀態分頁到磁碟。這個行為取決於狀態供應器實作。`Persisted` 設定的預設狀態供應器具有這個行為。

如果指定索引鍵的項目不存在，就可以使用會擲回 `KeyNotFoundException` 的標準 *Get* 作業來擷取狀態：

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

如果指定索引鍵的項目不存在，也可以使用不會擲回任何項目的 *TryGet* 方法來擷取狀態：

```csharp
class MyActor : Actor, IMyActor
{
    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```

### 儲存狀態

狀態管理員擷取方法會傳回本機記憶體中物件的參考。在本機記憶體中單獨修改此物件並不會永久儲存該物件。從狀態管理員中擷取並修改物件時，必須將它重新插入狀態管理員，才能永久儲存。

狀態可以使用無條件的 *Set* 來插入，這相當於 `dictionary["key"] = value` 語法︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

狀態可以使用 *Add* 方法來新增，其將會在嘗試新增已存在的索引鍵時擲回 `InvalidOperationException`︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

狀態也可以使用 *TryAdd* 方法來新增，其在嘗試新增已存在的索引鍵時不會擲回任何項目︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```

在動作項目方法結束時，狀態管理員會自動儲存透過插入或更新作業所新增或修改的任何值。根據所使用的設定，「儲存」可以包括保存到磁碟與複本。尚未經過修改的值不會保存或複寫。如果未修改任何值，儲存作業就不會有任何動作。萬一儲存失敗，就會捨棄修改的狀態並重新載入原始狀態。

狀態也可以呼叫動作項目基底上的 `SaveStateAsync` 方法來手動儲存︰

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);
            
    await this.SaveStateAsync();
}
```

### 移除狀態

狀態可以藉由呼叫 *Remove* 方法，從動作項目的狀態管理員中永久移除。這個方法將會在嘗試移除不存在的索引鍵時擲回 `KeyNotFoundException`︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

狀態也可以使用 *TryRemove* 方法永久移除，此方法在嘗試移除不存在的索引鍵時不會擲回任何項目︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```

## 後續步驟
 - [動作項目類型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
 - [動作項目多型和物件導向的設計模式](service-fabric-reliable-actors-polymorphism.md)
 - [動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
 - [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [範例程式碼](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->