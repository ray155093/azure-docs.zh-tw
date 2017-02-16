---
title: "以範例作為起始"
description: "有了 Power BI Embedded，即可使用 SDK 將互動式 Power BI 報告新增到您的商業智慧應用程式中"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2f0f36e7ffeec52bacc35ac5039cd183976dc3aa
ms.openlocfilehash: 9c9ab36ac90c3e73a21c7e53a6715cc4503a8e99


---
# <a name="get-started-with-power-bi-embedded-sample"></a>開始使用 Power BI Embedded 範例
運用 **Microsoft Power BI Embedded**，您可以將 Power BI 報告整合至您的 Web 應用程式或行動應用程式。 在本文中，會向您介紹 **Power BI Embedded** 開始使用範例。

在我們繼續之前，您可能想要儲存下列資源。 它們會協助您將 Power BI 報告整合至範例應用程式和您自己的應用程式。

* [範例儀表板 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI Embedded API 參考](https://msdn.microsoft.com/library/mt711493.aspx)
* [Power BI Embedded .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (透過 NuGet 提供)

> [!NOTE]
> 在您可以設定及執行 Power BI Embedded 開始使用範例之前，您需要在您的 Azure 訂用帳戶中至少建立一個「工作區集合」。 若要了解如何在 Azure 入口網站中建立**工作區集合**，請參閱[開始使用 Power BI Embedded](power-bi-embedded-get-started.md)。
>
>

## <a name="configure-the-sample-app"></a>設定範例應用程式
讓我們逐步引導您設定 Visual Studio 開發環境，以存取執行範例應用程式時所需的元件。

1. 下載並解壓縮 GitHub 上的 [Power BI Embedded - 將報表整合到 Web 應用程式中](http://go.microsoft.com/fwlink/?LinkId=761493) 範例。
2. 在 Visual Studio 中開啟 **PowerBI-embedded.sln** 。 您可能需要執行 NuGET 套件管理器主控台中的 **Update-Package** 以更新此方案中使用的套件。
3. 建置方案。
4. 執行 **ProvisionSample** 主控台應用程式。 您可以在範例主控台應用程式中，佈建工作區並匯入 PBIX 檔案。
5. 若要佈建新的**工作區**，請選取選項 5，[在現有的工作區集合中佈建新的工作區]。

    ![](media/powerbi-embedded-get-started-sample/console-option-5.png)
6. 輸入您的 [工作區集合] 名稱，以及 [存取金鑰]。 您可以在「Azure 入口網站」 中取得這些項目。 若要深入了解如何取得存取金鑰 ，請參閱＜開始使用 Microsoft Power BI Embedded＞中的 [檢視 Power BI API 存取金鑰](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) 。

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. 複製並儲存新建立的「工作區識別碼」  ，以供稍後在此文章中使用。 「工作區識別碼」建立之後，您就可以在「Azure 入口網站」中找到它。

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. 若要將 PBIX 檔案匯入到您的**工作區**，請選取選項**&6;。**\[將 PBIX Desktop 檔案匯入現有的工作區\]。 如果您現在沒有 PBIX 檔案，可以下載[零售分析範例 PBIX](http://go.microsoft.com/fwlink/?LinkID=780547)。
9. 如果出現提示，請輸入易記名稱做為您「資料集」 的名稱。

您應該會看到像這樣的回應：

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> If your PBIX file contains any direct query connections, run option 7 to update the connection strings.
>
>

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app
The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [!NOTE]
> This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.
>
>

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

| Part | Description |
| --- | --- |
| Title |Name of the Report. |
| QueryString |A link to the Report ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller
**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app
Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## Filter reports embedded in your application
You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  
>
>

## See also
* [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
* [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)



<!--HONumber=Jan17_HO1-->


