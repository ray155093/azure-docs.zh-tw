---
title: Azure Active Directory B2B 共同作業 | Microsoft Docs
description: Azure Active Directory B2B 共同作業可讓商務夥伴存取您的公司應用程式，且每位使用者都可用單一 Azure AD 帳戶來代表
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/23/2016
ms.author: curtand

---
# Azure Active Directory B2B 共同作業
Azure Active Directory (Azure AD) B2B 共同作業允許以合作夥伴管理的身分識別來存取您的公司應用程式。您可以建立跨公司的關聯性，邀請並授權合作夥伴公司的使用者存取您的資源。複雜性降低，因為每一家公司與 Azure Active Directory 只同盟一次，且每一位使用者都由單一 Azure AD 帳戶來代表。如果商務夥伴在 Azure AD 中管理其帳戶，則安全性會提高，因為當合作夥伴使用者從他們的組織中離職時，存取權即撤銷，絕不可能再透過內部目錄中的成員資格來存取。對於還沒有 Azure AD 的商務夥伴，B2B 共同作業有簡便的註冊體驗提供 Azure AD 帳戶給您的商務夥伴。

* 您的商務夥伴使用他們自己的登入認證，您不必管理外部合作夥伴目錄，也不需要在使用者離開合作夥伴組織時移除存取權。
* 您可以獨立管理您的應用程式，與商務夥伴的帳戶生命週期無關。例如，這表示您可以自行撤銷存取權，而不必要求商務夥伴的 IT 部門進行任何動作。

## 功能
B2B 共同作業簡化管理並改善合作夥伴存取公司資源時的安全性，包括 SaaS 應用程式 (例如 Office 365、Salesforce、Azure 服務)，以及每個行動、雲端和內部部署宣告感知應用程式。B2B 共同作業可讓合作夥伴管理自己的帳戶，企業可以將安全性原則套用至合作夥伴存取權。

Azure Active Directory B2B 共同作業的設定很簡單，各種規模的合作夥伴都能透過電子郵件驗證程序，輕鬆地完成註冊，即使他們沒有自己的 Azure Active Directory 也沒問題。維護也很容易，不需要外部目錄或個別合作夥伴的同盟設定。

## B2B 共同作業程序
1. Azure AD B2B 共同作業可讓公司系統管理員將一個不超過 2000 行的逗號分隔值 (CSV) 檔案上傳至 B2B 共同作業入口網站，就能邀請並授權一群外部使用者。
   
   ![CSV 檔案上傳對話方塊](./media/active-directory-b2b-collaboration-overview/upload-csv.png)
2. 入口網站會傳送電子郵件邀請給這些外部使用者。
3. 受邀的使用者將透過 Microsoft 登入現有的工作帳戶 (在 Azure AD 中管理)，或在 Azure AD 中取得新的工作帳戶。
4. 登入之後，使用者將重新導向至與他們共用的應用程式。

目前不支援邀請消費者電子郵件地址 (例如，Gmail 或 [*comcast.net*](http://comcast.net/))。

如需有關 B2B 共同作業運作方式的詳細資訊，請觀賞[這段影片](http://aka.ms/aadshowb2b)。

## 後續步驟
請瀏覽有關 Azure AD B2B 共同作業的其他文章。

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [運作方式](active-directory-b2b-how-it-works.md)
* [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
* [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
* [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
* [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

<!---HONumber=AcomDC_0928_2016-->