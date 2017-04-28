---
title: "想要將 Cortana Intelligence 應用程式發佈至 AppSource？ | Microsoft Docs"
description: "在 AppSource 發佈 Cortana Intelligence 或 Microsoft R Services 應用程式的逐步指南"
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>想要將 Cortana Intelligence 應用程式發佈至 AppSource？
在 AppSource 發佈 Cortana Intelligence 或 Microsoft R Services 應用程式的逐步指南

## <a name="1-introduction"></a>1.簡介

感謝您對將您的 Cortana Intelligence / Microsoft R 解決方案應用程式發佈到 Microsoft AppSource 感興趣。 Microsoft 很榮幸能與我們可靠的合作夥伴 (ISV / SI) 一同合作，提供客戶、轉銷商和實作合作夥伴一個最佳場所，讓他們能夠探索及嘗試商務解決方案，進而發揮投資的最大功效。 本指南逐步說明發佈您的解決方案的每個步驟。

## <a name="2-getting-started"></a>2.開始使用

遵循指示進行

1.  在[與 Microsoft 一同進行展示入門指南](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf)，了解如何列為進階分析合作夥伴。

2.  在 AppSource 填寫[列出應用程式](https://appsource.microsoft.com/partners/list-an-app)表單。
    - 針對「選擇您的應用程式為什麼產品打造」此問題，請選擇 [其他] 核取方塊，並在編輯控制中列出「Cortana Intelligence」。

## <a name="3-solution-evaluation-criteria"></a>3.解決方案評估準則

下列是應用程式必須符合的準則清單

1.  應用程式必須能在特定的功能領域內，以重複的方式，解決特定的商務使用案例問題。 該應用程式運用最基本的組態/自訂，便應能在短時間內，實現預先定義的價值主張。

2.  解決方案應至少使用下列分析元件之其中一種

    1. HDInsight
    2. 機器學習服務
    3. Data Lake Analytics
    4. 串流分析
    5. 辨識服務
    6. Bot Framework
    7. 獨立的 Microsoft R 伺服器或在 SQL 2016/HDInsight Premium 的 R 服務

3.  解決方案應至少產生 \$1 K / 月 / 客戶，使用數位合作夥伴記錄 (DPOR) 或雲端解決方案提供者 (CSP) 的程式。

4.  解決方案應使用 Azure Active Directory 同盟單一登入 (AAD 同盟 SSO)，並允許啟用使用者驗證和資源存取控制。 如果您的應用程式尚未啟用，請參閱

    1. [開始使用](https://identity.microsoft.com/)
    2. [整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  解決方案中使用 Power BI，並共用 Power BI desktop 檔案 (PBIX): 選擇性，但極為建議，因為經證實可以產生更高數量的潛在客戶

    1. 請確認所有資料皆匯入 PBIX 檔案
    2. 沒有外部參考資料 – 我們為您建立建立內嵌報告。

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4.決定您想要在 Appsoure 上發佈的優惠類型

AppSource 使合作夥伴能夠提供檢視者兩種類型的試用體驗
1.  客戶主導的試用，也就是客戶可以自行體驗應用程式。
    1.  免費試用
        1.  AppSource 能將使用者導覽至您 (合作夥伴) 提供的 URL，此 URL 帶領使用者經過 AAD 同盟 SSO，並提供據時間限制的試用體驗。
        2.  我們預期您的應用程式是 SaaS 應用程式，並具備多組織用戶支援，可將一名使用者的組態/資料與其他使用者隔離，或試用體驗只需要唯讀操作，並提供部分體驗。

        範例︰ 請參閱 [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)， [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (此應用程式針對特定的使用者角色集合，提供整合體驗及清楚路徑) 等。
    2.  試用產品
        1.  您 (合作夥伴) 的解決方案，或是其部分，可在合作夥伴 Azure 訂閱中使用 Azure Resource Manager 範本進行封裝，並以 AppSource 進行個體化及管理，並具備時間限制及維護執行個體熱/冷集區等。
        2.  如果您選擇此途徑，我們可以提供範本和範例程式碼來協助您。
        
        範例︰請參閱 Neal Analytics [清查最佳化](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)。

2.    合作夥伴主導的試用，需要客戶填寫連絡資訊表單，接著讓合作夥伴執行後續動作，提供示範/試用等。

請參閱 [AppSource 試用體驗逐步解說](http://aka.ms/trialexperienceforwebapps)影片，以取得高階概述。

相關資料清楚顯示，若與合作夥伴主導的試用相比，客戶主導的試用，擁有較高的潛在客戶產生潛力 – 想想看，誰會想要填寫表單😊

決定您想要新增至 Appsoure 的優惠類型。

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5.取得使用 Azure Marketplace 發佈入口網站的核准

現在 AAD/MSA 電子郵件識別碼需要經過核准，您才能存取 Azure Marketplace 發佈入口網站，並開始發佈。 請分享下列詳細資訊到 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)

-   發佈者顯示名稱︰&lt;顯示在 AppSource 上的公司名稱，即應用程式的發佈者&gt;

-   發佈者的唯一名稱︰&lt;僅使用小寫字母與連字號「-」的唯一名稱&gt;

-   網站︰&lt;公司網站&gt;

-   擁有人的電子郵件︰ &lt;AAD/MSA 電子郵件識別碼&gt;

-   參與者 2 的電子郵件︰ &lt;AAD/MSA 電子郵件識別碼&gt;

 如果有需要，新增其他參與者。

## <a name="6-get-info-about-your-companys-lead-management"></a>6.取得您公司的潛在客戶管理相關資訊

訪客對解決方案表達興趣，這些潛在客戶便會直接傳遞到合作夥伴的潛在客戶管理系統。 AppSource 支援多種潛在客戶管理系統 (Dynamics CRM、Salesforce、Marketo 等)。

請參閱 [AppSource 行銷潛在客戶管理](./cloud-partner-portal-get-customer-leads.md)，了解整體流程的細節，以及您如何找到正確資訊來設定 AppSource 潛在客戶。    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7.在發佈入口網站上發佈應用程式

如果您針對我們略過的任何欄位，需要進一步資訊，請傳送電子郵件到 <appsourcecissupport@microsoft.com>

請遵循下列步驟

1.  使用 Google Chrome 瀏覽器，開啟 [Cloud Partner 入口網站](https://cloudpartner.azure.com)

2.  使用白名單 AAD/MSA 電子郵件識別碼登入

3.  選取新的優惠 - &gt; Cortana Intelligence

    ![publishaaapp1][1]
4.  填寫優惠設定詳細資訊

    ![publishaaapp2][2]

    1. 優惠識別碼是用來在 AppSource 上唯一識別應用程式，會是唯一的 AppSource URL 的一部分。 範例：「appsource-saas-app」。 客戶會在 Azure Resource Manager 範本和計費報告等處看到此識別碼。 請注意，優惠識別碼和發佈者資訊在儲存後便無法變更。

    2. 請使用您的解決方案名稱做為優惠名稱。 請勿添加您的公司名稱，您的公司名稱會自動添加至解決方案名稱底下。

5.  填寫技術資訊區段 – 多數資訊一目瞭然無須多加解釋，並提供具有更多資訊的工具小訣竅。

    1.  示範影片應為實際解決方案功能的簡短編輯影片，強調使用者使用 AAD 進行驗證、使用者所將享有的主要功能，及可與 Cortana Intelligence 平台整合。  示範影片不會****公開提供給客戶，但要能具體呈現解決方案給潛在客戶。  因此，這些影片應備有腳本，並且能重複播放。

        您可以使用任何可以匯出標準影片格式 (例如 MPEG) 的畫面錄製工具，來準備您的示範影片。 以下說明，適合您如果擁有 商務用 Skype 的情況

        1.  [開始會議](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [共用您的桌面](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [開始錄製](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. 在您停止錄製後，[使用錄製管理員來發佈您的錄製影片](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

        您必須將錄製的影片上傳至服務，來讓您產生共享的 URL。  例如，您可以使用[OneDrive/Sharepoint 中的來賓連結](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)。 

    2.  請參閱[進階分析解決方案工作流程範本](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)，了解上傳方案架構圖表的準則

6.  填寫店面詳細資訊區段

    1.  附註：「優惠描述」與「使用條款」欄位中可填寫 html 內容。 您可以使用任何線上 html 編輯器，來調整您的 html 內容，直到您取得您想要的外觀。 優惠描述應能清楚扼要地提供銷售點，說明優惠能解決的商務案例、客戶將獲得的 ROI，以及任何能支援銷售點的相關舉證。

    2.  應用程式類型︰ 根據前面章節「決定您想要在 Appsoure 上發佈的優惠類型」，挑選選項
        1.  「免費」如果您想要提供客戶無時間限制完全免費的體驗。
        2.  「試用」如果您想要提供客戶具時間限制的試用/試用產品體驗
        3.  「要求試用版」如果您想要提供客戶合作夥伴主導的試用體驗。

    3.  隱藏金鑰，用來測試暫存中的優惠：暫存您的應用程式後，此金鑰可用來建立您應用程式的預覽 URL，並不是密碼，所以您可以用簡單的組合。 範例：「AppSourceHideKey」

    4.  為了讓優惠發佈可以通過核准，上傳的影像必須是高畫質可讀取影像。

    5.  案例研究︰提供具有下列細節資訊的案例研究/故事

        1.  客戶實作的實際運作
        2. 實際客戶或匿名客戶清楚定義的問題陳述
        3. 解決問題所需採取的步驟
        4. 參考 CI/Microsoft 進階分析元件，有助於解決問題。
        5.  建立解決方案的淨效益或 ROI
        6. 不符合要求的資料︰僅說明整體平台的行銷手冊或影片，卻沒有滿足上述需求

    6.  由於會產生更多潛在客戶，因此強烈建議︰AppSource 上顯示同樣符合案例研究要求的影片。 輸入影片的 URL 時，影片可以是 Youtube 影片或 vimeo 影片。 請確定格式是下列格式之一
        1. https://vimeo.com/########## 或
        2. https://www.youtube.com/watch?v=##########

7.  填寫連絡人區段，它將用來傳送使用通知，marketplace 警示等。

8.  發佈您的優惠。 解決方案送至驗證程序時，您將收到電子郵件通知。 您在優惠發佈 UI 的 [狀態] 標籤，可以看到狀態的詳細資訊。

    1.  首先會暫存您的解決方案。
    2.  您可以點擊從電子郵件中收到的 AppSource 連結，驗證您的暫存內容。
    3.  進行必要的變更，然後重新提交。 您對最後內容感到滿意時，請將應用程式推送至生產。
    4.  此時，我們會驗證應用程式的中繼資料，最後核准您的應用程式列在 AppSource，或我們拒絕您的應用程式，並提出拒絕原因。

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

