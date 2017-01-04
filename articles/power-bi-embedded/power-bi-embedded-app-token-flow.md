---
title: "使用 Power BI Embedded 驗證和授權"
description: "使用 Power BI Embedded 驗證和授權"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2b860b5815a0dd35138c685eb90490a8e2c53d5e


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>使用 Power BI Embedded 驗證和授權
Power BI Embedded 服務是使用**金鑰**和**應用程式權杖**進行驗證和授權，而不是使用明確的使用者驗證。 在此模型中，您的應用程式會管理使用者的驗證與授權。 然後，您的應用程式可以在必要時建立及傳送應用程式權杖，以告知我們的服務轉譯要求的報告。 雖然您仍然可以使用 Azure Active Directory 來進行使用者驗證與授權，但這項設計不需要您的應用程式這樣做。

## <a name="two-ways-to-authenticate"></a>兩種驗證方式
**金鑰** - 您可以針對所有 Power BI Embedded REST API 呼叫使用金鑰。 金鑰可以在 **Azure 入口網站** 中找到，方法是按一下[所有設定]，然後按一下 [存取金鑰]。 一律將您的金鑰視為密碼。 這些金鑰具有在特定工作區集合上呼叫任何 REST API 的權限。

若要在 REST 呼叫上使用金鑰，請新增下列授權標頭︰            

    Authorization: AppKey {your key}

**應用程式權杖** - 應用程式權杖用於所有內嵌的要求。 它們設計為在用戶端執行，因此限制為單一報告，且最佳做法是設定到期時間。

應用程式權杖是 JWT (JSON Web 權杖)，由您的其中一個金鑰簽署。

您的應用程式權杖可包含下列宣告：

| 宣告 | 說明 |
| --- | --- |
| **ver** |應用程式權杖的版本。 目前版本為 0.2.0。 |
| **aud** |權杖的預定接收者。 對於 Power BI Embedded，使用：“https://analysis.windows.net/powerbi/api”。 |
| **iss** |字串，表示已發出權杖的應用程式。 |
| **type** |正在建立的應用程式權杖類型。 目前唯一支援的類型為 **內嵌**。 |
| **wcn** |為其發出權杖的工作區集合名稱。 |
| **wid** |為其發出權杖的工作區識別碼。 |
| **rid** |為其發出權杖的報告識別碼。 |
| **username** (選擇性) |與 RLS 搭配使用，這是字串，可以在套用 RLS 規則時協助識別使用者。 |
| **角色** (選擇性) |字串，包含套用資料列層級安全性規則時要選取的角色。 如果傳遞多個角色，應該將它們傳遞為字串陣列。 |
| **exp** (選擇性) |指出權杖到期的時間。 這些項目應該傳遞為 Unix 時間戳記。 |
| **nbf** (選擇性) |指出權杖開始生效的時間。 這些項目應該傳遞為 Unix 時間戳記。 |

範例應用程式權杖看起來像這樣：

![](media/power-bi-embedded-app-token-flow/power-bi-embedded-app-token-flow-sample-coded.png)

解碼時，看起來像這樣：

![](media/power-bi-embedded-app-token-flow/power-bi-embedded-app-token-flow-sample-decoded.png)

## <a name="heres-how-the-flow-works"></a>以下是流程的運作方式
1. 將 API 金鑰複製到您的應用程式。 您可以在 **Azure 入口網站**取得金鑰。
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. 權杖判斷提示宣告，並有到期時間。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. 以 API 存取金鑰簽署權杖。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. 使用者要求檢視報告。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. 以 API 存取金鑰驗證權杖。
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded 將報表傳送給使用者。
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

當 **Power BI Embedded** 將報表傳送給使用者之後，使用者就可以在您自訂的應用程式中檢視報表。 例如，如果您匯入了 [分析銷售資料 PBIX 範例](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)，範例 Web 應用程式看起來就會像這樣︰

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>另請參閱
* [開始使用 Microsoft Power BI Embedded 範例](power-bi-embedded-get-started-sample.md)
* [Microsoft Power BI Embedded 常見案例](power-bi-embedded-scenarios.md)
* [開始使用 Microsoft Power BI Embedded](power-bi-embedded-get-started.md)




<!--HONumber=Nov16_HO3-->


