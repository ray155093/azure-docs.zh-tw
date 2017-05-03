---
title: "在 Azure 中搭配 SQL Database 建立 ASP.NET 應用程式 | Microsoft Docs"
description: "了解如何取得在 Azure 中運作的 ASP.NET 應用程式，並連接至 SQL Database。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/07/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d7006a50d35412021f7e475df526661854b23dc8
ms.lasthandoff: 04/22/2017


---
# <a name="create-an-aspnet-app-in-azure-with-sql-database"></a>在 Azure 中搭配 SQL Database 建立 ASP.NET 應用程式

本教學課程示範如何在 Azure 中開發資料導向的 ASP.NET Web 應用程式、將它連接到 Azure SQL Database，並啟用您的資料導向功能。 完成時，您的 ASP.NET 應用程式將會在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中執行，並已連接到 SQL Database。

![已在 Azure Web 應用程式中發佈的 ASP.NET 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

## <a name="before-you-begin"></a>開始之前

執行此範例之前，請先[下載並安裝免費的 Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例
在此步驟中，您要下載範例 ASP.NET 應用程式。

### <a name="get-the-sample-project"></a>取得範例專案

按一下[這裡](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)以下載範例專案。

將下載的 `dotnet-sqldb-tutorial-master.zip` 解壓縮到工作目錄。

> [!TIP]
> 您可以藉由複製 GitHub 存放庫來取得相同的範例專案：
>
> ```bash
> git clone https://github.com/Azure-Samples/dotnet-sqldb-tutorial.git
> ```
>
>

這個範例專案包含一個簡單的 [ASP.NET MVC](https://www.asp.net/mvc) CRUD (建立-讀取-更新-刪除) 應用程式，其建置於 [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 上。

### <a name="run-the-application"></a>執行應用程式

在 Visual Studio 2017 中，從解壓縮的目錄啟動 `dotnet-sqldb-tutorial-master\DotNetAppSqlDb.sln`。

在開啟範例方案之後，輸入 `F5`，在瀏覽器中執行該方案。

您應該會在首頁中看到簡單的待辦事項清單。 嘗試在空的清單中加入數個待辦事項。

![[新增 ASP.NET 專案] 對話方塊](./media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

您的資料庫內容會使用名為 `MyDbConnection` 的連接字串。 此連接字串定義於 `Web.config` 中，並在 `Models\MyDatabaseContext.cs` 中加以參考。 當您稍後將 Azure Web 應用程式連接到 Azure SQL Database 時，只需用到此連接字串名稱。 

## <a name="publish-to-azure-with-sql-database"></a>發佈至含有 SQL Database 的 Azure

在 [方案總管] 中，以滑鼠右鍵按一下 [DotNetAppSqlDb] 專案，然後選取 [發佈]。

![從方案總管發佈](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

確定已選取 [Microsoft Azure App Service]，然後按一下 [發佈]。

![從專案概觀頁面發佈](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

這會開啟 [建立 App Service] 對話方塊，協助您建立在 Azure 中執行 ASP.NET Web 應用程式所需的所有 Azure 資源。

### <a name="sign-in-to-azure"></a>登入 Azure

在 [建立 App Service] 對話方塊中，按一下 [新增帳戶]，然後登入您的 Azure 訂用帳戶。 如果您已登入 Microsoft 帳戶，請確定該帳戶保留您的 Azure 訂用帳戶。 如果登入的 Microsoft 帳戶沒有 Azure 訂用帳戶，請按一下它來新增正確的帳戶。
   
![登入 Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

登入之後，您即可在此對話方塊中建立 Azure Web 應用程式需要的所有資源。

### <a name="create-a-resource-group"></a>建立資源群組

首先，您需有_資源群組_。 

> [!NOTE] 
> 資源群組是一個邏輯容器，可在其中部署與管理 Azure 資源 (例如 Web 應用程式、資料庫和儲存體帳戶)。
>
>

按一下 [資源群組] 旁邊的 [新增]。

將您的資源群組命名為 **myResourceGroup**，然後按一下 [確定]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

Azure Web 應用程式也需要「App Service 方案」。 

> [!NOTE]
> App Service 方案代表用來裝載應用程式的實體資源集合。 所有指派給 App Service 方案的應用程式都會共用它所定義的資源，而讓您節省裝載多個應用程式時的成本。 
>
> App Service 方案可定義：
>
> - 區域 (北歐、美國東部、東南亞)
> - 執行個體大小 (小型、中型、大型)
> - 級別計數 (一、二或三個執行個體等) 
> - SKU (免費、共用、基本、標準、進階)
>
>

按一下 [App Service 方案] 旁邊的 [新增]。 

在 [設定 App Service 方案] 對話方塊中，使用下列設定來設定新的 App Service 方案︰

- **App Service 方案**：輸入 **myAppServicePlan**。 
- **位置**︰選擇 [西歐]，或您喜歡的任何其他區域。
- **大小**︰選擇 [免費]，或您喜歡的任何其他[定價層](https://azure.microsoft.com/pricing/details/app-service/)。

按一下 [確定] 。

![建立 App Service 方案](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

### <a name="configure-the-web-app-name"></a>設定 Web 應用程式名稱

在 [Web 應用程式名稱] 中，輸入唯一的應用程式名稱。 此名稱將做為應用程式預設 DNS 名稱的一部分 (`<app_name>.azurewebsites.net`)，所以在 Azure 的所有應用程式中必須是唯一的名稱。 您稍後先將自訂網域名稱對應至您的應用程式，再將它公開給使用者。

您也可以接受自動產生的名稱 (已是唯一的)。

若要準備下一個步驟，請按一下 [探索其他 Azure 服務]。

![設定 Web 應用程式名稱](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

### <a name="create-a-sql-server-instance"></a>建立 SQL Server 執行個體

在 [服務] 索引標籤中，按一下 [SQL Database] 旁的 [+] 圖示。 

在 [設定 SQL Database] 對話方塊中，按一下 [SQL Server] 旁的 [新增]。 

在 [應用程式名稱] 中，輸入唯一的名稱。 此名稱將用來做為資料庫伺服器預設 DNS 名稱的一部分 (`<server_name>.database.windows.net`)，因此，在 Azure 的所有 SQL Server 執行個體中必須是唯一的名稱。 

以您喜歡的方式設定其餘的欄位，然後按一下 [確定]。

![建立 SQL Server 執行個體](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

### <a name="configure-the-sql-database"></a>設定 SQL Database

在 [資料庫名稱] 中，輸入 `myToDoAppDb` 或您喜歡的任何名稱。

在 [連接字串名稱] 中，輸入 `MyDbConnection`。 此名稱必須符合 `Models\MyDatabaseContext.cs` 中所參考的連接字串。

![設定 SQL Database](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

### <a name="create-and-publish-the-web-app"></a>建立和發佈 Web 應用程式

按一下 [建立] 。 

當精靈完成建立 Azure 資源時，就會自動將 ASP.NET 應用程式首度發佈至 Azure，然後在預設瀏覽器中啟動已發佈的 Azure Web 應用程式。

嘗試在空的清單中新增數個待辦事項。

![已在 Azure Web 應用程式中發佈的 ASP.NET 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

恭喜！ 您的資料導向 ASP.NET 應用程式正在 Azure App Service 中執行。

## <a name="access-the-sql-database-locally"></a>在本機存取 SQL Database

Visual Studio 可讓您在 [SQL Server 物件總管] 中，輕鬆地探索和管理新的 SQL Database。

### <a name="create-a-database-connection"></a>建立資料庫連接

輸入 `Ctrl`+`\`、`Ctrl`+`S`，來開啟 [SQL Server 物件總管]。

在 [SQL Server 物件總管] 上方，按一下 [加入 SQL Server] 按鈕。

### <a name="configure-the-database-connection"></a>設定資料庫連接

在 [連接] 對話方塊中，展開 [Azure] 節點。 此處會列出 Azure 中您所有的 SQL Database。

選取您稍早建立的 SQL Database。 系統會自動在下方填入您稍早使用的連接。

輸入您先前使用的資料庫管理員密碼，然後按一下 [連接]。

![從 Visual Studio 設定資料庫連接](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>允許來自您電腦的用戶端連接

[建立新的防火牆規則] 對話方塊即會開啟。 根據預設，您的 SQL Server 執行個體僅允許來自 Azure 服務 (例如 Azure Web 應用程式) 的連接。 若要直接從 Visual Studio 連接到您的資料庫，您需要在 SQL Server 執行個體中建立防火牆規則，以允許本機電腦的公用 IP 位址。

在 Visual Studio 中，要這樣做很簡單。 對話方塊中已經填入您的電腦公用 IP 位址。

確定已選取 [加入我的用戶端 IP]，然後按一下 [確定]。 

![設定 SQL Server 執行個體的防火牆](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

當 Visual Studio 完成建立 SQL Server 執行個體的防火牆設定時，您的連接就會出現在 [SQL Server 物件總管] 中。

您可以在此處執行最常見的資料庫作業，例如執行查詢、建立檢視表和預存程序，以及其他更多作業。 下列範例示範如何檢視資料庫資料。 

![探索 SQL Database 物件](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>使用 Code First 移轉更新應用程式

在此步驟中，您將使用 Entity Framework 中的 Code First 移轉來變更資料庫結構描述，然後將它發佈至 Azure。

### <a name="update-your-data-model"></a>更新資料模型

在程式碼編輯器中，開啟 `Models\Todo.cs`。 將下列屬性加入至 `ToDo` 類別：

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>在本機執行 Code First 移轉

接下來，執行數個命令以進行 localdb 資料庫的更新。 

從 [工具] 功能表中，按一下 [NuGet 封裝管理員] > [封裝管理員主控台]。 此主控台通常會在下方視窗中開啟。

啟用 Code First 移轉，如下：

```PowerShell
Enable-Migrations
```

加入移轉，如下：

```PowerShell
Add-Migration AddProperty
```

更新 localdb 資料庫，如下：

```PowerShell
Update-Database
```

透過 `F5` 執行應用程式，以測試您的變更。

如果應用程式載入而不會出現錯誤，則表示 Code First 移轉已成功。 不過，您的頁面仍然看起來一樣，因為您的應用程式邏輯尚未使用這個新屬性。 

### <a name="use-the-new-property"></a>使用新屬性

讓我們在您的程式碼中進行一些變更以使用 `Done` 屬性。 為了簡單起見，在本教學課程中，您僅需變更 `Index` 和 `Create` 檢視，以查看作用中的屬性。

開啟 `Controllers\TodosController.cs`。

尋找 `Create()` 方法，並將 `Done` 加入至 `Bind` 屬性 (Attribute) 中的屬性 (Property) 清單。 完成時，您的 `Create()` 方法簽章應該如下所示：

```csharp
public ActionResult Create([Bind(Include = "id,Description,CreatedDate,Done")] Todo todo)
```

開啟 `Views\Todos\Create.cshtml`。

在 Razor 程式碼中，您應該會看到使用 `model.Description` 的 `<div class="form-group">` 標記，然後是另一個使用 `model.CreatedDate` 的 `<div class="form-group">` 標記。 在這兩個標記的正後方，加入另一個使用 `model.Done` 的 `<div class="form-group">` 標記，如下：

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

開啟 `Views\Todos\Index.cshtml`。

搜尋空白的 `<th></th>` 標記。 在此標記的正上方，加入下列 Razor 程式碼：

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

尋找包含 `Html.ActionLink()` Helper 方法 的 `<td>` 標記。 在此標記的正上方，加入下列 Razor 程式碼：

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

您只需查看 `Index` 和 `Create` 檢視中的變更。 

再次輸入 `F5` 以執行應用程式。

您應該能夠立即加入待辦事項，並勾選 [完成]。 然後，它應該會在您的首頁中顯示為已完成的項目。 請記住，這是您現在唯一可以做的動作，因為您並未變更 `Edit` 檢視。

### <a name="enable-code-first-migrations-in-azure"></a>啟用 Azure 中的 Code First 移轉

您的程式碼變更會立即運作 (包括資料庫移轉)，您也可以將它發佈至 Azure Web 應用程式，然後使用 Code First 移轉來更新您的 SQL Database。

就像之前一樣，以滑鼠右鍵按一下專案，然後選取 [發佈]。

按一下 [設定] 以開啟發佈精靈。

![開啟發佈設定](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

在精靈中，按 [下一步]。

確定 [MyDatabaseContext (MyDbConnection)] 中已填入您的 SQL Database 連接字串。 您可能需要從下拉式清單中選取 [myToDoAppDb] 資料庫。 

選取 [執行 Code First 移轉 (在應用程式啟動時執行)] ，然後按一下 [儲存]。

![在 Azure Web 應用程式中啟用 Code First 移轉](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>發佈您的變更

現在，您已在 Azure Web 應用程式中啟用 Code First 移轉，只需發佈您的程式碼變更即可。

在發佈頁面中，按一下 [發佈]。

嘗試再次建立新的待辦事項，然後選取 [完成]，而它們應該會在您的首頁中顯示為已完成的項目。

![Code First 移轉之後的 Azure Web 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

> [!NOTE]
> 請注意，仍會顯示您現有的所有待辦事項。 當您重新發佈 ASP.NET 應用程式時，您 SQL Database 中現有的資料不會遺失。 此外，Code First 移轉只會變更資料結構描述，並讓現有的資料保持不變。
>
>

## <a name="stream-application-logs"></a>資料流應用程式記錄

您可以將追蹤訊息從 Azure Web 應用程式直接資料流處理到 Visual Studio。

開啟 `Controllers\TodosController.cs`。

請注意，每個動作都是以 `Trace.WriteLine()` 方法開始。 加入此程式碼的用意是，示範將追蹤訊息加入至 Azure web 應用程式是多麼容易。

### <a name="open-server-explorer"></a>開啟伺服器總管

您可以在 [伺服器總管] 中設定 Azure Web 應用程式的記錄。 

若要開啟它，請輸入 `Ctrl`+`Alt`+`S`。

### <a name="enable-log-streaming"></a>啟用記錄資料流

在 [伺服器總管] 中，展開 [Azure] > [App Service]。

展開 [myResourceGroup] 資源群組，這是您在第一次建立 Azure Web 應用程式時所建立的資源群組。

以滑鼠右鍵按一下您的 Azure Web 應用程式，然後選取 [檢視資料流記錄]。

![啟用記錄資料流](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

現在已將記錄資料流處理至 [輸出] 視窗。 

![[輸出] 視窗中的記錄資料流](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

不過，您還不會看到任何追蹤訊息。 這是因為當您第一次選取 [檢視資料流記錄] 時，您的 Azure Web 應用程式會將追蹤層級設定為 `Error`，這只會記錄錯誤事件 (使用 `Trace.TraceError()` 方法)。

### <a name="change-trace-levels"></a>變更追蹤層級

若要變更追蹤層級以輸出其他追蹤訊息，請返回 [伺服器總管]。

再次以滑鼠右鍵按一下您的 Azure Web 應用程式，然後選取 [設定]。

在 [應用程式記錄 (檔案系統)] 下拉式清單中，選取 [詳細資訊]。 按一下 [儲存] 。

![將追蹤層級變更為詳細資訊](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 您可以試驗不同的追蹤層級，以查看針對每個層級顯示的訊息類型。 例如，[資訊] 層級包含 `Trace.TraceInformation()`、`Trace.TraceWarning()` 和`Trace.TraceError()` 所建立的所有記錄，但不包含 `Trace.WriteLine()` 所建立的記錄。
>
>

在瀏覽器中，嘗試在 Azure 中的待辦事項清單應用程式附近按一下。 追蹤訊息現在會資料流處理到 Visual Studio 的 [輸出] 視窗中。

```
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>停止記錄資料流

若要停止記錄資料流服務，按一下 [輸出] 視窗中的 [停止監視] 按鈕。

![停止記錄資料流](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>管理您的 Azure Web 應用程式

請移至 Azure 入口網站，以查看您所建立的 Web 應用程式。 

若要這麼做，請登入 [https://portal.azure.com](https://portal.azure.com)。

按一下左側功能表中的 [App Service]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

您已進入 Web 應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。 

根據預設，Web 應用程式的刀鋒視窗會顯示 [概觀] 頁面。 此頁面可讓您檢視應用程式的執行方式。 您也可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。 刀鋒視窗左側的索引標籤會顯示您可開啟的各種設定頁面。 

![Azure 入口網站中的 App Service 刀鋒視窗](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

刀鋒視窗中的索引標籤會顯示您可以新增至 Web 應用程式的許多強大功能。 下表提供幾個可能性︰

- 對應自訂 DNS 名稱
- 繫結自訂 SSL 憑證
- 設定連續部署
- 相應增加和相應放大
- 新增使用者驗證

## <a name="next-steps"></a>後續步驟

瀏覽預先建立的 [Web 應用程式 PowerShell 指令碼](app-service-powershell-samples.md)。
