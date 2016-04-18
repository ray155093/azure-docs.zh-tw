<properties
   pageTitle="Power BI Embedded .NET API"
   description="Power BI Embedded .NET API"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Power BI Embedded .NET API

**Microsoft Power BI Embedded** Preview 主要著重於將大部分的現有 Power BI API 功能公開為 Azure 服務的一部分，以讓您用來開發應用程式。此外，您還可以透過程式設計方式佈建、開發和部署所需的資源和 Power BI 內容。

您可以使用 **Power BI API** 來建立和管理 Power BI 內容工作區。使用 API，您可以

  - 使用金鑰型驗證，將 Power BI Desktop 檔案 (PBIX) 匯入工作區中。
  - 修改資料集的連接字串。
  - 取得要整合至您專屬應用程式的**報表**。
  - 設定認證，讓資料集可與正確的來源資料來源交談。
  - 若要深入了解 **Power BI API**，請參閱[開始使用 Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)。

請參閱 [Power BI Reference on MSDN](https://msdn.microsoft.com/library/mt669800.aspx) (MSDN 上的 Power BI 參考)。

以下是 **Power BI Embedded 範例**中所使用的一些類別和方法：

## Microsoft.PowerBI.Api.Beta 命名空間

### ReportsExtensions 類別
|名稱|說明
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|取得所指定工作區內可用的報表清單
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|取得所指定工作區內可用的報表清單

## Microsoft.PowerBI.Security 命名空間

### PowerBIToken 方法
|名稱| 說明
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|建立具有預設到期時間的開發人員權杖，以用來存取 Power BI 平台服務
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|建立具有預設到期時間的佈建權杖，以用來管理工作區集合內的工作區

## 另請參閱

- [何謂 Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [開始使用 Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->