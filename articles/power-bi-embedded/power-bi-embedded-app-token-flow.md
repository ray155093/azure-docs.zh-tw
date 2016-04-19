<properties
   pageTitle="關於 Power BI Embedded 的應用程式權杖流程"
   description="關於 Power BI Embedded 的應用程式權杖流程"
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

# 關於 Power BI Embedded 的應用程式權杖流程

**Power BI Embedded** 服務是使用**應用程式權杖**進行驗證和授權，而不是使用明確的使用者驗證。在**應用程式權杖**模型中，您的應用程式會管理您使用者的驗證與授權。然後，您的應用程式可以在必要時建立及傳送**應用程式權杖**，以告知我們的服務轉譯要求的報告。雖然您可以使用 **Azure Active Directory** 來進行使用者驗證與授權，但這項設計不需要您的應用程式這樣做。

**以下是應用程式權杖金鑰流程的運作方式**

1. 將 API 金鑰複製到您的應用程式。您可以在 **Azure 入口網站**取得索引鍵。

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. 權杖判斷提示宣告，並有到期時間。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. 以 API 存取金鑰簽署權杖。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. 使用者要求檢視報告。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	以 API 存取金鑰驗證權杖。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded 將報表傳送給使用者。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

當 **Power BI Embedded** 將報表傳送給使用者之後，使用者就可以在您自訂的應用程式中檢視報表。例如，如果您匯入了[分析銷售資料 PBIX 範例](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)，範例 Web 應用程式看起來就會像這樣︰

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## 另請參閱
- [開始使用 Microsoft Power BI Embedded 範例](power-bi-embedded-get-started-sample.md)
- [何謂 Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Microsoft Power BI Embedded Preview 常見案例](power-bi-embedded-scenarios.md)
- [開始使用 Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->