---
title: "將報告內嵌在 Azure Power BI Embedded | Microsoft Docs"
description: "了解如何將 Power BI Embedded 中的報告內嵌到您的應用程式。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3d865af2418c9c557c861a379766a125d75cebf1
ms.lasthandoff: 03/14/2017


---
# <a name="embed-a-report-in-power-bi-embedded"></a>將報告內嵌在 Power BI Embedded

了解如何將 Power BI Embedded 中的報告內嵌到您的應用程式。

我們將探討如何將報告實際內嵌到您的應用程式。 本文假設您在工作區集合的某個工作區內已存有報告。 如果您尚未完成該步驟，請參閱[開始使用 Power BI Embedded](power-bi-embedded-get-started.md)。

您可以使用 .NET (C#) 或 Node.js SDK 以及 JavaScript 來輕鬆建置含有 Power BI Embedded 的應用程式。 

## <a name="using-the-access-keys-to-use-rest-apis"></a>使用存取金鑰來使用 REST API

若要呼叫 REST API，您可以傳遞給定工作區集合的存取金鑰 (可從 Azure 入口網站取得)。 如需詳細資訊，請參閱[開始使用 Power BI Embedded](power-bi-embedded-get-started.md)。

## <a name="get-a-report-id"></a>取得報告識別碼

每個存取權杖都會以報告為基礎。 您必須針對想要內嵌的報告取得給定的報告識別碼。 這可以根據[取得報告](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API 的呼叫來完成。 這會傳回報告識別碼和內嵌 URL。 這可以使用 Power BI .NET SDK 或直接呼叫 REST API 來完成。

### <a name="using-the-power-bi-net-sdk"></a>使用 Power BI .NET SDK

在使用 .NET SDK 時，您必須建立以取自 Azure 入口網站的存取金鑰為基礎的權杖認證。 這需要您安裝 [Power BI API NuGet 套件](https://www.nuget.org/profiles/powerbi)。

**NuGet 套件安裝**

```
Install-Package Microsoft.PowerBI.Api
```

**C# 程式碼**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>直接呼叫 REST API

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>建立存取權杖

Power BI Embedded 使用內嵌權杖，這是 HMAC 簽署的 JSON Web 權杖。 權杖會使用來自 Azure Power BI Embedded 工作區集合的存取金鑰進行簽署。 內嵌權杖依預設可用來提供要內嵌至應用程式之報告的唯讀存取權。 內嵌權杖會針對特定報告來核發，而且應該與內嵌 URL 相關聯。

存取權杖應該建立在伺服器上，因為會使用存取金鑰來簽署/加密權杖。 如需如何建立存取權杖的相關資訊，請參閱[使用 Power BI Embedded 驗證和授權](power-bi-embedded-app-token-flow.md)。 您也可以檢閱 [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) 方法。 以下是使用 .NET SDK for Power BI 時此方法所呈現樣貌的範例。

您會使用先前擷取到的報告識別碼。 建立了內嵌權杖後，您接著會使用存取金鑰來產生可從 JavaScript 觀點來使用的權杖。 要使用 PowerBIToken 類別，您必須安裝 [Power BI 核心 NuGut 套件](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)。

**NuGet 套件安裝**

```
Install-Package Microsoft.PowerBI.Core
```

**C# 程式碼**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>在內嵌權杖新增權限範圍

在使用內嵌權杖時，您可能會想限制您授與了存取權之資源的使用量。 為此，您可以產生具有範圍權限的權杖。 如需詳細資訊，請參閱[範圍](power-bi-embedded-app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>使用 JavaScript 進行內嵌

擁有存取權杖和報告識別碼後，我們可以使用 JavaScript 來內嵌報告。 這需要您安裝 NuGet [Power BI JavaScript 套件](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)。 embedUrl 會是 https://embedded.powerbi.com/appTokenReportEmbed。

> [!NOTE]
> 您可以使用 [JavaScript 報告內嵌範例](https://microsoft.github.io/PowerBI-JavaScript/demo/)來測試功能。 它也會提供可用之不同作業的程式碼範例。

**NuGet 套件安裝**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript 程式碼**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>設定內嵌元素大小

報告會根據其容器的大小自動內嵌。 若要覆寫預設的內嵌大小，只需新增 CSS 類別屬性或寬度和高度的內嵌樣式即可。

## <a name="see-also"></a>另請參閱

[開始使用範例](power-bi-embedded-get-started-sample.md)  
[在 Power BI Embedded 中驗證和授權](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript 內嵌範例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript 套件](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet 套件](https://www.nuget.org/profiles/powerbi)
[Power BI 核心 NuGut 套件](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git 存放庫](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git存放庫](https://github.com/Microsoft/PowerBI-Node)  
有其他疑問？ [試用 Power BI 社群](http://community.powerbi.com/)

