
---
title: 條件式存取 - 支援哪些應用程式 | Microsoft Docs
description: 透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。 一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: markvi

---
# <a name="conditional-access-support-for-applications"></a>應用程式的條件式存取支援
Azure Active Directory 連線應用程式、預先整合的同盟 SaaS 應用程式、使用密碼單一登入的應用程式、商務營運應用程式及 Azure AD 應用程式 Proxy，都支援條件式存取規則。 如需可啟用條件式存取的應用程式詳細清單，請參閱 [已啟用條件式存取功能的服務](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access)。 條件式存取可以與運用新式驗證的行動和傳統型應用程式搭配使用。 本主題說明這些應用程式的行動和桌面版本所支援的相關功能。

使用新式驗證的應用程式會顯示 Azure AD 登入頁面。 這讓使用者可以看見多重要素驗證的內嵌提示，或顯示使用者正面臨存取遭到封鎖的訊息。 此外，還需要新式驗證，裝置才能夠向 Azure AD 進行驗證，所以會評估裝置型條件式存取原則。

請務必了解支援哪些應用程式，以及可能需要用來保護其他進入點的步驟。

## <a name="applications-using-modern-authentication"></a>使用新式驗證的應用程式
下列應用程式支援在存取 Office 365 和其他 Azure AD 連接的服務應用程式時的條件式存取︰

| 目標服務 | 平台 | 應用程式 |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |郵件/行事曆/連絡人應用程式、Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證) |
| Office 365 Exchange Online |Windows 7、Windows 8.1 |Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證) |
| Office 365 Exchange Online |iOS、Android |Outlook 行動應用程式 |
| Office 365 Exchange Online |Mac OSX |僅限 MFA/位置的 Outlook 2016；未來提供的裝置型原則支援、未來提供的商業用 Skype 支援 |
| Office 365 SharePoint Online |Windows 10 |Office 2016 應用程式、Office Universal 應用程式、Office 2013 (已啟用新式驗證)、未來提供的商務用 OneDrive 應用程式 (NGSC 或新一代同步處理用戶端) 支援、未來提供的 Office Groups 支援、未來提供的 SharePoint 應用程式支援 |
| Office 365 SharePoint Online |Windows 7、Windows 8.1 |Office 2016 應用程式、Office 2013 (已啟用新式驗證)、商務用 OneDrive 應用程式 (Groove 同步處理用戶端) |
| Office 365 SharePoint Online |iOS、Android |Office 行動應用程式 |
| Office 365 SharePoint Online |Mac OSX |僅限 MFA/位置的 Office 2016 應用程式；未來提供的裝置型原則支援 |
| Office 365 Yammer |Windows 10、iOS 和 Android |Office Yammer 應用程式 |
| Dynamics CRM |Windows 10、7、8.1、iOS 和 Android |Dynamics CRM 應用程式 |
| PowerBI service |Windows 10、7、8.1、iOS 和 Android |PowerBI 應用程式 |
| Azure 遠端應用程式服務 |Windows 10、7、8.1、iOS 和 Android、Mac OSX |Azure 遠端應用程式 |
| 任何 My Apps 應用程式服務 |Android 和 iOS |任何 My Apps 應用程式服務 |

## <a name="applications-that-do-not-use-modern-authentication"></a>未使用新式驗證的應用程式
您目前必須使用其他方法來封鎖存取未使用新式驗證的應用程式，因為無法依條件式存取強制執行它們。 這主要是對於 Exchange 和 SharePoint 存取的考量，因為舊版應用程式是使用較舊的通訊協定來建置。

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online
您可以使用 Set-SPOTenant Cmdlet，在 SharePoint 中停用舊版通訊協定。 這個 Cmdlet 將阻止未使用新式驗證通訊協定的 Office 用戶端存取 SharePoint Online 資源。 

**範例命令**：    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

## <a name="office-365-exchange-online"></a>Office 365 Exchange Online
Exchange 上有兩個主要的通訊協定類別，請檢閱並選取貴組織適用的正確原則：

1. Exchange ActiveSync。 根據預設，不會針對 Exchange ActiveSync 強制套用 MFA 和位置的條件式存取原則。 這樣就能藉由直接設定 Exchange ActiveSync 原則或使用 AD FS 規則封鎖 Exchange ActiveSync 來保護存取。
2. 舊版通訊協定。 您可以在 AD FS 上封鎖舊版通訊協定。 這將會封鎖對於舊版 Office 用戶端的存取，例如，未啟用新式驗證的 Office 2013 及更早版本。

### <a name="example-ad-fs-rules"></a>AD FS 規則範例
下列規則可在兩種常見的組態中，用來封鎖 AD FS 上的舊版通訊協定存取。

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>選項 1︰允許 Exchange ActiveSync，並且只允許內部網路上的舊版應用程式
藉由將下列三個規則套用到適用於 Microsoft Office 365 識別身分平台的 AD FS 信賴憑證者信任，就能允許 Exchange ActiveSync 流量，以及瀏覽器和新式驗證流量。 來自外部網路的舊版應用程式將會遭到封鎖。 

規則 1

    `@RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

規則 2

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

規則 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>選項 2︰允許 Exchange ActiveSync 並封鎖舊版應用程式
藉由將下列三個規則套用到適用於 Microsoft Office 365 識別身分平台的 AD FS 信賴憑證者信任，就能允許 Exchange ActiveSync 流量，以及瀏覽器和新式驗證流量。 來自任何位置的舊版應用程式將會遭到封鎖。 

規則 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


規則 2 

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


規則 3 

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");











<!--HONumber=Oct16_HO2-->


