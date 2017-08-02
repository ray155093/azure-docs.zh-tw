---
title: "在 Azure Active Directory 中使用條件式存取規則的應用程式和瀏覽器 | Microsoft Docs"
description: "透過條件式存取控制，Azure Active Directory 會在驗證使用者以及允許存取應用程式時，檢查特定的條件。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 38c8cb00b21416add28eeb06187b89f64518dc92
ms.contentlocale: zh-tw
ms.lasthandoff: 07/19/2017

---
# <a name="applications-and-browsers-that-use-conditional-access-rules-in-azure-active-directory"></a>在 Azure Active Directory 中使用條件式存取規則的應用程式和瀏覽器

Azure Active Directory (Azure AD) 連線應用程式、預先整合的同盟軟體即服務 (SaaS) 應用程式、使用密碼單一登入 (SSO) 的應用程式、商務營運應用程式以及使用 Azure AD 應用程式 Proxy 的應用程式，都支援條件式存取規則。 如需可使用條件式存取的應用程式詳細清單，請參閱[已啟用條件式存取功能的服務](active-directory-conditional-access-technical-reference.md)。 條件式存取可以與運用新式驗證的行動和傳統型應用程式搭配使用。 在本文中，我們將討論條件式存取功能在行動和桌面應用程式中的運作方式。

您可以在使用新式驗證的應用程式中使用 Azure AD 登入頁面。 在登入頁面中，系統會提示您進行 Multi-Factor Authentication。 如果使用者的存取遭到封鎖，則會顯示訊息。 需要新式驗證，裝置才能夠向 Azure AD 進行驗證，以便評估裝置型條件式存取原則。

請務必知道哪些應用程式可以使用條件式存取規則，以及您可能需要採取哪些步驟來保護其他應用程式進入點。

## <a name="applications-that-use-modern-authentication"></a>使用新式驗證的應用程式
> [!NOTE]
> 如果 Azure AD 中的條件式存取原則等同於 Office 365 中的條件式存取原則，請設定這兩個條件式存取原則。 這適用於 Exchange Online 或 SharePoint Online 等的條件式存取原則。
>
>

下列應用程式支援 Office 365 和其他 Azure AD 連線服務應用程式的條件式存取︰


| 目標服務| 平台| 應用程式 |
| --- | --- | --- |
| 任何 My Apps 應用程式服務| Android 和 iOS| 應用程式的 MFA 和位置原則。 不支援裝置型原則。 |
| Azure 遠端應用程式服務| Windows 10、Windows 8.1、Windows 7、iOS、Android 和 Mac OS X| Azure 遠端應用程式|
| Dynamics CRM| Windows 10、Windows 8.1、Windows 7、iOS 和 Android| Dynamics CRM 應用程式|
| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS/Android 和 MAC OSX| Microsoft Teams Services - 這會控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務 - Windows 桌面、MAC OS X、iOS、Android、WP 和 Web 用戶端|
| Office 365 Exchange Online| Windows 10| 郵件/行事曆/連絡人應用程式、Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)|
| Office 365 Exchange Online| Windows 8.1、Windows 7| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)|
| Office 365 Exchange Online| iOS| Outlook 行動應用程式|
| Office 365 Exchange Online| Mac OS X| 僅限 Multi-Factor Authentication/位置的 Outlook 2016；未來規劃的裝置型原則支援、未來規劃的商業用 Skype 支援|
| Office 365 SharePoint Online| Windows 10| Office 2016 應用程式、通用 Office 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))、預計未來提供的 Office Groups 支援、預計未來提供的 SharePoint 應用程式支援|
| Office 365 SharePoint Online| Windows 8.1、Windows 7| Office 2016 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|
| Office 365 SharePoint Online| iOS、Android| Office 行動應用程式|
| Office 365 SharePoint Online| Mac OS X| 僅限 Multi-Factor Authentication 和位置的 Office 2016 應用程式；未來規劃的裝置型原則支援|
| Office 365 Yammer| Windows 10、iOS、Android| Office Yammer 應用程式|
| PowerBI service| Windows 10、Windows 8.1、Windows 7 及 iOS| PowerBI 應用程式。 適用於 Android 的 Power BI 應用程式目前不支援裝置型條件式存取。|
| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS 和 Android| Visual Studio Team Services 應用程式|







## <a name="applications-that-do-not-use-modern-authentication"></a>未使用新式驗證的應用程式
目前，您必須使用其他方法來阻止存取未使用新式驗證的應用程式。 條件式存取不會強制執行未使用新式驗證之應用程式的存取規則。 這主要是 Exchange 和 SharePoint 存取的考量。 大部分舊版應用程式使用較舊的存取控制通訊協定。

### <a name="control-access-in-office-365-sharepoint-online"></a>Office 365 SharePoint Online 中的控制存取
您可以使用 Set-SPOTenant Cmdlet，停用 SharePoint 存取的舊版通訊協定。 利用這個 Cmdlet，阻止未使用新式驗證通訊協定的 Office 用戶端存取 SharePoint Online 資源。

**範例命令**：`Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Office 365 Exchange Online 中的控制存取
Exchange 提供兩個主要的通訊協定類別。 檢閱下列選項，然後選取最適合貴組織的原則。

* **Exchange ActiveSync**。 根據預設，不會針對 Exchange ActiveSync 強制執行 Multi-Factor Authentication 和位置的條件式存取原則。 您需要直接設定 Exchange ActiveSync 原則或使用 Active Directory Federation Services (AD FS) 規則封鎖 Exchange ActiveSync，藉此保護這些服務的存取。
* **舊版通訊協定**。 您可以使用 AD FS 來封鎖舊版通訊協定。 這會封鎖對於舊版 Office 用戶端的存取，例如，未啟用新式驗證的 Office 2013 及更早的 Office 版本。

### <a name="use-ad-fs-to-block-legacy-protocol"></a>使用 AD FS 來封鎖舊版通訊協定
您可以使用下列範例發行授權規則，在 AD FS 層級封鎖舊版通訊協定存取。 從兩個常見的組態中進行選擇。

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>選項 1︰允許 Exchange ActiveSync，並且允許舊版應用程式 (但僅限內部網路)
將下列三個規則套用到適用於 Microsoft Office 365 身分識別平台的 AD FS 信賴憑證者信任，Exchange ActiveSync 流量以及瀏覽器和新式驗證流量就會有存取權。 來自外部網路的舊版應用程式會遭到封鎖。

##### <a name="rule-1"></a>規則 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>規則 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>規則 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>選項 2︰允許 Exchange ActiveSync，並且封鎖舊版應用程式
將下列三個規則套用到適用於 Microsoft Office 365 身分識別平台的 AD FS 信賴憑證者信任，Exchange ActiveSync 流量以及瀏覽器和新式驗證流量就會有存取權。 來自任何位置的舊版應用程式會遭到封鎖。

##### <a name="rule-1"></a>規則 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>規則 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>規則 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


## <a name="supported-browsers-for-device-based-policies"></a>裝置型原則的支援瀏覽器 

您只能取得針對 Azure AD 可以識別及驗證裝置時檢查裝置相容性和網域加入之裝置型原則的存取權。 大部分的檢查 (例如位置和 MFA) 是在大部分裝置和瀏覽器上運作，所以裝置原則需要以下所列的 OS 版本和瀏覽器。 當裝置原則已就緒，則會封鎖不受支援之瀏覽器或作業系統上的使用者存取。 

| 作業系統                     | 瀏覽器                 | 支援     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE、Edge                 | ![勾選][1] |
| Win 10                 | Chrome                   | 預覽     |
| Win 8 / 8.1            | IE、Chrome               | ![勾選][1] |
| Win 7                  | IE、Chrome               | ![勾選][1] |
| iOS                    | Safari                   | ![勾選][1] |
| Android                | Chrome                   | ![勾選][1] |
| Windows Phone          | IE、Edge                 | ![勾選][1] |
| Windows Server 2016    | IE、Edge                 | ![勾選][1] |
| Windows Server 2016    | Chrome                   | 敬請期待 |
| Windows Server 2012 R2 | IE、Chrome               | ![勾選][1] |
| Windows Server 2008 R2 | IE、Chrome               | ![勾選][1] |
| Mac OS                 | Safari                   | ![勾選][1] |
| Mac OS                 | Chrome                   | 敬請期待 |

> [!NOTE]
> 針對 Chrome 支援，您必須使用 Windows 10 建立者更新，而且安裝可以在[這裡](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)找到的擴充功能。
>
>

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure Active Directory 中的條件式存取](active-directory-conditional-access.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-supported-apps/ic195031.png



