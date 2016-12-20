---
title: "Azure Active Directory B2B 共同作業預覽版的 CSV 檔案格式 | Microsoft Docs"
description: "Azure Active Directory B2B 可讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: bd2c5364-4164-407d-ac25-34088c175c4a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ed32c23b8c69664dd75eae9c831341c93e57ebb3


---
# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Azure AD B2B 共同作業預覽：CSV 檔案格式
預覽版本的 Azure AD B2B 共同作業需要指定合作夥伴使用者資訊的 CSV 檔案透過 Azure AD 入口網站上傳。 CSV 檔案應該包含下列必要的標籤，以及視需要包含選擇性欄位。 修改範例 CSV 檔案 (如下) 而不變更第一個資料列中的標籤拼字。

> [!NOTE]
> 需要標籤的第一列 (例如電子郵件、DisplayName 等等)，才能成功剖析 CSV 檔案。 拼字必須符合以下指定的欄位。 這些標籤會識別在其下的內容。 對於不必要的選擇性欄位，可以從 CSV 檔案移除它們的標籤。 整個資料行可以是空白。
> 
> 

## <a name="required-fields-br"></a>必要欄位： <br/>
**Email：**受邀使用者的電子郵件地址。 <br/>
**DisplayName：**受邀使用者的顯示名稱 (通常是名字和姓氏)。<br/>

## <a name="optional-fields-br"></a>選擇性欄位： <br/>
**InvitationText：**自訂在應用程式商標後、兌換連結前的邀請電子郵件文字。<br/>
**InvitedToApplications：**用以指派使用者的公司應用程式 AppID。 在 PowerShell 中呼叫 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`，即可擷取 AppID。<br/>
**InvitedToGroups：**要將使用者新增到其中的群組 ObjectID。 在 PowerShell 中呼叫 `Get-MsolGroup | fl DisplayName, ObjectId`，即可擷取 ObjectID。<br/>
**InviteRedirectURL：**在受邀使用者接受邀請後，讓使用者前往的 URL。 這應該是公司特定的 URL (例如 [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))。 如果未指定這個選擇性欄位，受邀使用者會被導向至應用程式存取面板，他們可以在那裡瀏覽至您所選擇的公司應用程式。 「應用程式存取面板 URL」的格式為 `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`。<br/>
**CcEmailAddress**：要複製電子郵件邀請函的電子郵件地址。 如果使用 CcEmailAddress 欄位，此邀請函就不能用來建立以電子郵件驗證的使用者或租用戶。<br/>
**Language：**邀請函電子郵件和兌換體驗所用的語言，未指定時，預設值為 "en" (英文)。 其他 10 種支援的語言代碼是︰<br/>

1. de：德文<br/>
2. es：西班牙文<br/>
3. fr：法文<br/>
4. it：義大利文<br/>
5. ja：日文<br/>
6. ko：韓文<br/>
7. pt-BR：葡萄牙文 (巴西)<br/>
8. ru：俄文<br/>
9. zh-HANS：簡體中文<br/>
10. zh-HANT：繁體中文<br/>

## <a name="sample-csv-file"></a>CSV 檔案範例
以下是您可以修改的範例 CSV 範例。

> [!NOTE]
> 將這個項目複製並貼上至 [記事本]，然後以 '.csv' 副檔名儲存。 然後在 Excel 中編輯它。 它會結構化成為在第一個資料列中具有標籤的資料表。
> 
> 在 Excel 中新增更多選擇性欄位，方法是指定標籤及填入其下方的資料行。
> 
> 

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>相關文章
請瀏覽有關 Azure AD B2B 共同作業的其他文章

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [運作方式](active-directory-b2b-how-it-works.md)
* [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
* [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
* [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


