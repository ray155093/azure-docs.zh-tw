<properties
   pageTitle="Microsoft Power BI Embedded - 使用 IFrame 內嵌 Power BI 報告"
   description="Microsoft Power BI Embedded - 將報告整合到您的應用程式的必要程式碼、如何使用 Power BI Embedded 應用程式權杖進行驗證、如何取得報告"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# 使用 IFrame 內嵌 Power BI 報告
本文說明使用 **Microsoft Power BI Embedded** REST API、應用程式權杖、IFrame 和一些 JavaScript 的必要程式碼，以將報告整合或內嵌到您的應用程式。

在[開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)，了解如何設定**工作區集合**，為報告內容保存一或多個**工作區**。然後，在[開始使用 Microsoft Power BI Embedded 範例](power-bi-embedded-get-started-sample.md)，將報告匯入**工作區**。

在本文中，我們會詳細解說將報告內嵌至您的應用程式的步驟。若要依照這篇文章，您應該下載 GitHub 上的[整合報告與 IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) 範例。這個範例是簡單的 ASP.NET Web Form 應用程式，用來說明您整合報告所需的基本 C# 和 JavaScript 程式碼。如需使用 Model-View-Controller (MVC) 設計模式以整合報告的更進階範例，請參閱 GitHub 上的[範例儀表板 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=761493)。

為了整合報告，我們將會經歷下列步驟︰

- 步驟 1：[在工作區中取得報告](#GetReport)。在此步驟中，您可以使用應用程式權杖流程取得存取權杖，來呼叫 [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 作業。一旦您從 **Get Reports** 清單中取得報告，您使用 **IFrame** 元素將報告內嵌到應用程式。
- 步驟 2：[將報告內嵌到應用程式](#EmbedReport)。在此步驟中，您使用報告、一些 JavaScript 和 IFrame 的內嵌權杖，將報告整合或內嵌至 Web 應用程式。

如果您想要執行範例，以查看如何整合報告，請下載 GitHub 上的[整合報告與 IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) 範例，並且設定三個 Web.Config 設定︰

- **AccessKey**：**AccessKey** 是用來產生 JSON Web 權杖 (JWT)，它是用來取得報告與內嵌報告。若要了解如何取得 **AccessKey**，請參閱[開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)。
- **WorkspaceName**：若要了解如何取得 **WorkspaceName**，請參閱[開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)。
- **WorkspaceId**：若要了解如何取得 **WorkspaceId**，請參閱[開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)。

下一個章節會顯示整合報告所需的程式碼。

<a name="GetReport"/>
## 在工作區中取得報告

若要將報告整合到應用程式，您需要報告**識別碼**和 **embedUrl**。若要取得報告**識別碼**和 **embedUrl**，您呼叫 [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 作業，並從 JSON 清單中選擇報告。在[將報告內嵌到應用程式](#EmbedReport) 中，您使用報告**識別碼**和 **embedUrl**，將報告內嵌至您的應用程式。

### 取得報告 JSON 回應
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

若要呼叫 [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST 作業，請使用應用程式權杖。若要深入了解應用程式權杖流程，請參閱[關於 Power BI Embedded 的應用程式權杖流程](power-bi-embedded-app-token-flow.md)。下列程式碼說明如何取得報告的 JSON 清單。若要內嵌報告，請參閱[將報告內嵌到應用程式](#EmbedReport)。

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## 將報告內嵌到應用程式

在您可以將報告內嵌到您的應用程式之前，您需要報告的內嵌權杖。此權杖與用來呼叫 **Power BI Embedded** REST 作業的應用程式權杖類似，但是會針對報告資源產生，而不是針對 REST 資源。以下是取得報告的應用程式權杖的程式碼。若要使用報告應用程式權杖，請參閱[將報告內嵌到您的應用程式](#EmbedReportJS)。

<a name="EmbedReportToken"/>
### 取得報告的應用程式權杖

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### 將報告內嵌到您的應用程式

若要將 **Power BI** 報告內嵌到您的應用程式，請使用 IFrame 和一些 JavaScript 程式碼。以下是內嵌報告的範例 IFrame 和 JavaScript 程式碼。若要查看內嵌報告的所有範例程式碼，請參閱 GitHub 上的[整合報告與 IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) 範例。

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
在報告內嵌到您的應用程式之後，您就可以篩選此報告。下一節說明如何使用 URL 語法篩選報告。

## 篩選報告

您可以使用 URL 語法，篩選內嵌的報表。若要這樣做，請將查詢字串參數加入含指定篩選的 iFrame src URL。您可以 [依值篩選] 和 [隱藏篩選窗格]。


**依值篩選**

若要依某個值篩選，請使用 **$filter** 查詢語法搭配 **eq** 運算子，如下所示︰

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

例如，您可以篩選 Store Chain 是 'Lindseys' 的地方。url 的篩選部分如下所示：

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {表格名稱/欄位名稱} 不能包含空格或特殊字元。{欄位值} 接受單一類別目錄值。

**隱藏篩選窗格**

若要隱藏 [篩選窗格]，請將 **filterPaneEnabled** 加入至報告查詢字串，如下所示︰

```
&filterPaneEnabled=false
```

## 其他資源

在本文中，為您介紹將 **Power BI** 報告整合到您的應用程式的程式碼。請務必查看 GitHub 上的其他範例︰

- [整合報告與 IFrame 範例](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [範例儀表板 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=761493)

## 另請參閱
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0713_2016-->