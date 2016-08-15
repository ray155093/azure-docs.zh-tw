<properties
	pageTitle="Azure Batch 中的簡易應用程式安裝和管理功能 |Microsoft Azure"
	description="使用 Azure Batch 的應用程式封裝功能輕鬆地管理多個應用程式和版本，以便安裝在 Batch 計算節點。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/30/2016"
	ms.author="marsma" />

# 使用 Azure Batch 應用程式封裝部署應用程式

Azure Batch 的應用程式封裝功能可為集區中的計算節點提供簡單的應用程式管理和部署能力。透過應用程式封裝，您可以上傳和管理多個版本的應用程式，包括您的工作所執行的二進位檔和其支援檔案。接著，您可以將一或多個這種類型的應用程式自動部署到集區中的計算節點。

在本文中，您將了解如何使用 Azure 入口網站上傳和管理應用程式封裝。然後，您將了解如何使用 [Batch .NET][api_net] 程式庫將封裝安裝在集區的計算節點上。

> [AZURE.NOTE] 此處所述的應用程式封裝功能取代了舊版服務中的「Batch Apps」功能。

## 應用程式封裝需求

本文所討論的應用程式封裝功能「僅」能與 2016 年 3 月 10 日後建立的 Batch 集區相容。應用程式封裝將無法部署到在此日期之前建立之集區中的計算節點。

[Batch REST API][api_rest] 2015-12-01.2.2 版和對應的 [Batch .NET][api_net] 程式庫 3.1.0 版引進這項功能。使用 Batch 時，我們建議您一律使用最新的 API 版本。

> [AZURE.IMPORTANT] 目前，只有「CloudServiceConfiguration」集區支援應用程式封裝。您無法在使用 VirtualMachineConfiguration 映像建立的集區中使用應用程式封裝。如需這兩種不同組態的詳細資訊，請參閱[在 Azure Batch 集區中佈建 Linux 計算節點](batch-linux-nodes.md)的[虛擬機器組態](batch-linux-nodes.md#virtual-machine-configuration)一節。

## 關於應用程式和應用程式封裝

在 Azure Batch 中，「應用程式」是指一組已建立版本的二進位檔，這些檔案可自動下載到集區中的計算節點。「應用程式封裝」指的是這些二進位檔的「特定組合」，其代表應用程式的特定「版本」。

![應用程式和應用程式封裝的高階圖表][1]

### 應用程式

Batch 中的應用程式包含一或多個應用程式封裝，並且會指定應用程式的組態選項。例如，應用程式會指定要安裝在計算節點上的預設應用程式封裝版本，以及應用程式的封裝是否可以更新或刪除。

### 應用程式封裝

應用程式封裝為 .zip 檔案，其中包含工作執行所需的應用程式二進位檔和支援檔案。每個應用程式封裝都代表特定版本的應用程式。當您在 Batch 服務中建立集區時，您可以指定這些應用程式之中的一個或多個，以及 (選擇性) 指定版本。這些應用程式封裝會自動下載並解壓縮到每個節點上 (當節點加入集區時)。

> [AZURE.IMPORTANT] Batch 帳戶中的應用程式和應用程式封裝數目，以及應用程式封裝的大小上限有其限制。如需這些限制的詳細資料，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md)。

### 應用程式封裝的優點

應用程式封裝可以簡化 Batch 解決方案中的程式碼，以及降低工作所執行之應用程式的必要管理成本。

透過應用程式封裝，集區的啟動工作不需要指定在節點上安裝一長串的個別資源檔案。您不需要在 Azure 儲存體中或在節點上手動管理這些檔案的多個版本。再者，您也不必費心產生 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) 來提供這些檔案在 Azure 儲存體帳戶中的存取權限。

Batch 會在 Azure 儲存體的背景中運作，以將應用程式封裝儲存及部署到計算節點，從而簡化程式碼和管理成本。

## 上傳及管理應用程式

在 Azure 入口網站中，您可以新增、更新和刪除應用程式封裝。您可以設定每個應用程式的預設版本。

在接下來的幾個小節中，我們會先說明如何讓儲存體帳戶與 Batch 帳戶產生關聯，然後再說明如何檢閱 Azure 入口網站所提供的封裝管理功能。之後，您將了解如何使用 [Batch .NET][api_net] 程式庫將這些封裝部署到計算節點。

### 連結儲存體帳戶

若要使用應用程式封裝，您必須先將 Azure 儲存體帳戶連結到 Batch 帳戶。如果您還沒有為 Batch 帳戶設定儲存體帳戶，Azure 入口網站會在您第一次按一下 [Batch 帳戶] 刀鋒視窗中的 [應用程式] 圖格時顯示警告。

> [AZURE.IMPORTANT] Batch 目前「只」支援**一般用途**的儲存體帳戶類型，如[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的步驟 5 [建立儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)所述。當您將 Azure 儲存體帳戶連結至 Batch 帳戶時，請「只」連結**一般用途**的儲存體帳戶。

![Azure 入口網站中的未設定儲存體帳戶警告][9]

Batch 服務會在應用程式封裝的儲存和擷取作業中使用相關聯的儲存體帳戶。在連結兩個帳戶之後，Batch 便能將儲存在連結之儲存體帳戶中的封裝自動部署到計算節點。按一下 [警告] 刀鋒視窗中的 [儲存體帳戶設定]，然後按一下 [儲存體帳戶] 刀鋒視窗上的 [儲存體帳戶]，將現有的儲存體帳戶連結至 Batch 帳戶。

![Azure 入口網站中的選擇儲存體帳戶刀鋒視窗][10]

我們建議您建立「專門」用來與 Batch 帳戶搭配使用的儲存體帳戶，並在此處選取它。如需如何建立儲存體帳戶的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的＜建立儲存體帳戶＞。在建立儲存體帳戶之後，您可以使用 [儲存體帳戶] 刀鋒視窗，將它連結到 Batch 帳戶。

> [AZURE.WARNING] 由於 Batch 會使用 Azure 儲存體來儲存應用程式封裝，所以我們會針對區塊 Blob 資料向您收取[標準費用][storage_pricing]。請務必考量應用程式封裝的大小和數目，並定期移除過時的封裝以降低成本。

### 檢視目前的應用程式

若要檢視 Batch 帳戶中的應用程式，請按一下 [Batch 帳戶] 刀鋒視窗中的 [應用程式] 圖格。

![應用程式圖格][2]

這會開啟 [應用程式] 刀鋒視窗︰

![列出應用程式][3]

[應用程式] 刀鋒視窗會顯示帳戶中每個應用程式的識別碼，以及下列屬性︰

* **封裝**--與此應用程式相關聯的版本號碼。
* **預設版本**--如果您在設定集區的應用程式時未指定版本，系統會安裝此版本。這項設定是選擇性的。
* **允許更新**--此值會指定是否允許更新、刪除和新增封裝。如果此值設為 [否]，應用程式會停用封裝的更新和刪除，而只能新增新的應用程式封裝版本。預設值為 [是]。

### 檢視應用程式詳細資料

在 [應用程式] 刀鋒視窗中按一下應用程式，就能開啟包含該應用程式詳細資料的刀鋒視窗。

![應用程式詳細資料][4]

在應用程式詳細資料刀鋒視窗中，您可以配置應用程式的以下設定。

* **允許更新**--指定是否可更新或刪除應用程式的應用程式封裝。請參閱本文稍後的＜更新或刪除應用程式封裝＞。
* **預設版本**--指定要部署至計算節點的預設應用程式封裝。
* **顯示名稱**--指定 Batch 解決方案在顯示應用程式相關資訊時 (例如，在透過 Batch 提供給客戶之服務的 UI 中)，可以使用的「易記」名稱。

### 加入新的應用程式

若要建立新應用程式，請新增應用程式封裝並指定新的唯一應用程式識別碼。使用新應用程式識別碼加入的第一個應用程式封裝也會建立新的應用程式。

按一下 [應用程式] 刀鋒視窗中的 [加入]，以開啟 [新增應用程式] 刀鋒視窗。

![Azure 入口網站中的新增應用程式刀鋒視窗][5]

[新增應用程式] 刀鋒視窗提供以下欄位，供您指定新應用程式和應用程式封裝的設定。

**應用程式識別碼**

此欄位能指定新應用程式的識別碼，其須符合標準 Azure Batch 識別碼驗證規則的規範︰

* 可包含英數字元的任何組合，包括連字號和底線。
* 不能包含超過 64 個字元。
* 在 Batch 帳戶內必須是唯一的。
* 保留大小寫且不區分大小寫。

**版本**

指定要上傳的應用程式封裝版本。版本字串須符合以下驗證規則的規範︰

* 可包含英數字元的任何組合，包括連字號、底線和句點。
* 不能包含超過 64 個字元。
* 在應用程式內必須是唯一的。
* 保留大小寫且不區分大小寫。

**應用程式封裝**

此欄位指定包含應用程式執行所需之應用程式二進位檔和支援檔案的 .zip 檔案。按一下 [選取檔案] 方塊或資料夾圖示，以瀏覽及選取包含應用程式檔案的 .zip 檔案。

在選取檔案之後，請按一下 [確定] 以開始上傳到 Azure 儲存體。當上傳作業完成時，系統會通知您且刀鋒視窗會關閉。因上傳之檔案的大小和網路連線速度不盡相同，這項作業可能需要花費一些時間。

> [AZURE.WARNING] 上傳作業完成之前，請勿關閉 [新增應用程式] 刀鋒視窗。否則上傳程序將會停止。

### 加入新應用程式封裝

若要加入現有應用程式的新應用程式封裝版本，請在 [應用程式] 刀鋒視窗中選取應用程式，然後依序按一下 [封裝] 和 [加入] 以開啟 [加入封裝] 刀鋒視窗。

![Azure 入口網站中的加入應用程式封裝刀鋒視窗][8]

如您所見，欄位與 [新增應用程式] 刀鋒視窗中的欄位相符，但 [應用程式識別碼] 方塊已停用。依照和新增應用程式相同的方式，指定新封裝的 [版本]，瀏覽至您的**應用程式封裝** .zip 檔案，然後按一下 [確定] 以上傳封裝。

### 更新或刪除應用程式封裝

若要更新或刪除現有的應用程式封裝，請開啟應用程式的詳細資料刀鋒視窗，按一下 [封裝] 以開啟 [封裝] 刀鋒視窗，按一下要修改之應用程式封裝資料列中的**省略符號**，然後選取要執行的動作。

![在 Azure 入口網站中更新或刪除封裝][7]

**更新**

當您按一下 [更新] 時，[更新封裝] 刀鋒視窗隨即出現。此刀鋒視窗與 [新增應用程式封裝] 刀鋒視窗相似，只不過已啟用封裝選取欄位，因此您可以指定要上傳的新 ZIP 檔案。

![Azure 入口網站中的更新封裝刀鋒視窗][11]

**刪除**

當您按一下 [刪除] 時，系統會要求您確認要刪除封裝版本，隨後 Batch 會從 Azure 儲存體中刪除該封裝。如果您刪除應用程式的預設版本，系統會移除應用程式的 [預設版本] 設定。

![刪除應用程式][12]

## 將應用程式安裝在計算節點上

我們已說明如何使用 Azure 入口網站上傳及管理應用程式封裝，接下來我們可以討論如何將它們實際部署到計算節點，並使用 Batch 工作予以執行。

若要將應用程式封裝安裝在集區中的計算節點上，您需要為集區指定一或多個應用程式封裝「參考」。在 Batch .NET 中，將一或多個 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] 新增到您建立的新集區或新增到現有集區。

[ApplicationPackageReference][net_pkgref] 類別能指定要安裝在集區之計算節點上的應用程式識別碼和版本。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

您針對集區指定的應用程式封裝會在每個計算節點加入集區時，以及該節點重新啟動或重新安裝映像時，安裝於該節點上。如果應用程式封裝部署基於任何因素而失敗，Batch 服務會將節點標示為[無法使用][net_nodestate]，而且不會在該節點上排程任何要執行的工作。在此情況下，您應該**重新啟動**節點以重新初始化封裝部署。重新啟動節點也會在節點上再次啟用工作排程。

## 執行安裝的應用程式

當每個計算節點加入集區、重新啟動或重新安裝映像時，您指定的封裝會下載並解壓縮到節點上 `AZ_BATCH_ROOT_DIR` 內指定的目錄中。Batch 也會為您的工作命令列建立環境變數，以在呼叫應用程式二進位檔時使用。此變數會遵守下列命名配置︰

`AZ_BATCH_APP_PACKAGE_appid#version`

例如，如果您指定要安裝應用程式「Blender」的 2.7 版，您的工作可以透過在其命令列中參考下列環境變數來存取應用程式二進位檔：

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

如果應用程式指定預設版本，您可以參考不含版本字串尾碼的環境變數。例如，如果您已在 Azure 入口網站中指定「Blender」應用程式的預設版本 2.7，您的工作可以參考下列環境變數：

`AZ_BATCH_APP_PACKAGE_BLENDER`

下列程式碼片段示範當您已指定「Blender」應用程式的預設版本時，該如何設定工作。

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] 如需計算節點環境設定的詳細資訊，請參閱 [Batch 功能概觀](batch-api-basics.md)的＜工作的環境設定＞。

## 更新集區的應用程式封裝

如果您已設定現有集區的應用程式封裝，可以指定集區的新封裝。如果您為集區指定新的封裝參考，則會適用下列情況：

* 加入集區的所有新節點和任何重新啟動或重新安裝映像的現有節點都會安裝新指定的封裝。
* 在更新封裝參考時已存在集區中的計算節點不會自動安裝新應用程式封裝。這些計算節點必須重新啟動或重新安裝映像才能接收新封裝。
* 部署新的封裝之後，所建立的環境變數會反映新的應用程式封裝參考。

在此範例中，現有集區已將「Blender」應用程式的 2.7 版設定為其中一個 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]。若要將集區的節點更新為 2.76b 版，請將 [ApplicationPackageReference][net_pkgref] 指定為新版本並認可變更。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

既然您已設定新版本，任何加入集區的「新」節點都會部署 2.76b 版。若要將 2.76b 安裝在「已存在」集區中的節點上，請將節點重新啟動或重新安裝映像。請注意，重新啟動的節點會保留前次封裝部署的檔案。

## 列出 Batch 帳戶中的應用程式

您可以使用 [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 方法列出 Batch 帳戶中的應用程式和應用程式封裝。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## 總結

透過應用程式封裝，您可以協助客戶選取其作業適用的應用程式，以及指定在以啟用 Batch 功能的服務處理作業時所要使用的確切版本。您也可以在服務中提供讓客戶上傳及追蹤其應用程式的功能。

## 後續步驟

* [Batch REST API][api_rest] 也提供應用程式封裝的使用支援。例如，請參閱[將集區加入至帳戶][rest_add_pool]中的 [applicationPackageReferences][rest_add_pool_with_packages] 項目，以取得如何使用 REST API 來指定要安裝之封裝的相關資訊。如需如何使用 Batch REST API 來取得應用程式資訊的詳細資料，請參閱[應用程式][rest_applications]。

* 了解如何以程式設計方式[使用 Batch Management .NET 管理 Azure Batch 帳戶和配額](batch-management-dotnet.md)。[Batch Management .NET][api_net_mgmt] 程式庫可以啟用 Batch 應用程式或服務的帳戶建立和刪除功能。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "應用程式封裝高階圖表"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure 入口網站中的應用程式圖格"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure 入口網站中的應用程式刀鋒視窗"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure 入口網站中的應用程式詳細資料刀鋒視窗"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure 入口網站中的新增應用程式刀鋒視窗"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure 入口網站中的更新或刪除封裝下拉式清單"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure 入口網站中的新增應用程式封裝刀鋒視窗"
[9]: ./media/batch-application-packages/app_pkg_09.png "沒有連結的儲存體帳戶警示"
[10]: ./media/batch-application-packages/app_pkg_10.png "Azure 入口網站中的選擇儲存體帳戶刀鋒視窗"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure 入口網站中的更新封裝刀鋒視窗"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure 入口網站中的刪除封裝確認對話方塊"

<!---HONumber=AcomDC_0803_2016-->