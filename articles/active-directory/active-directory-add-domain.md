<properties
	pageTitle="將您的自訂網域名稱新增至 Azure Active Directory | Microsoft Azure"
	description="如何在 Azure Active Directory 中新增您公司的網域名稱，以及如何確認網域名稱。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# 將您的自訂網域名稱新增至 Azure Active Directory

您已有一或多個您的組織用來經營業務的網域名稱，而且您的使用者使用您公司的網域名稱來登入公司網路。既然您將要使用 Azure Active Directory (Azure AD)，您也可以將您的公司網域名稱加入 Azure AD。這可讓您在目錄中指派您的使用者熟悉的使用者名稱，例如 ‘alice@contoso.com’。 程序佷簡單：

- 在 Azure 傳統入口網站的 [新增網域] 精靈中新增您的網域名稱

- 在 Azure AD 傳統入口網站或 Azure AD Connect 工具中取得 DNS 項目

- 在 DNS 註冊機構的網站上，於 DNS 區域檔案中新增網域名稱的 DNS 項目

- 在 Azure AD 傳統入口網站或 Azure AD Connect 工具中確認網域名稱


在您確認您的自訂網域名稱之前，您的使用者必須使用 ‘alice@contoso.onmicrosoft.com’ 之類的使用者名稱來登入，這類名稱會使用您目錄的初始網域名稱。如果您需要多個自訂網域名稱，例如 ‘contoso.com’ 和 ‘contosobank.com’，您最多可以新增 900 個網域名稱。請使用本文中的相同步驟來新增每個網域名稱。

## 在目錄中新增自訂網域名稱

1. 使用屬於 Azure AD目錄全域管理員的使用者帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2. 在 [Active Directory] 中開啟您的目錄，然後選取 [網域] 索引標籤。

3. 在命令列上選取 [新增]，然後輸入自訂網域的名稱，例如 'contoso.com'。請務必包含 .com、.net 或其他最上層的擴充。

4. 如果您打算對使用內部部署 Active Directory 的[同盟登入](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)設定此網域，請選取該核取方塊。

5. 選取 [新增]。

既然您已新增網域名稱，Azure AD 必須確認您的組織擁有該網域名稱。您必須先在 DNS 區域檔案中新增該網域名稱的 DNS 項目，Azure AD 才可以進行此確認。這項工作是在該網域名稱的網域名稱註冊機構網站上進行。

## 取得網域名稱的 DNS 項目

如果您未與內部部署的 Windows Server Active Directory 同盟，DNS 項目會位於 [新增網域] 精靈的第二頁上。

如果您要設定同盟網域，系統會將您導向以便下載 Azure AD Connect 工具。請執行 Azure AD Connect 工具以便[取得您需要在網域名稱註冊機構新增的 DNS 項目](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。Azure AD Connect 工具也會確認 Azure AD 的網域名稱。

## 在 DNS 區域檔案中新增 DNS 項目

1.  登入網域的網域名稱註冊機構。如果您的權限不足以更新 DNS 項目，請要求具有此存取權的人員或小組新增 DNS 項目。

2.  透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。此 DNS 項目可讓 Azure AD 確認您擁有網域。DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。傳播 DNS 記錄最多可能需要一個小時的時間。

[在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## 使用 Azure AD 確認網域名稱

新增了 DNS 項目後，您必須確保網域名稱已由 Azure AD 確認過。這是成功之前的最後一個步驟。

如果您的 [新增網域] 精靈仍保持開啟，請選取精靈第三頁上的 [確認]。在進行確認之前，最多請等候一小時來讓 DNS 項目傳播。

如果 [新增網域] 精靈未保持開啟，您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中確認網域。

1.  使用屬於 Azure AD 目錄全域管理員的使用者帳戶進行登入。

2.  開啟您的目錄，然後選取 [網域] 索引標籤。

3.  選取您想要確認的網域。

4.  選取命令列上的 [確認]，然後選取對話方塊中的 [確認]。

恭喜，您已成功！ 現在您可以[指派包含自訂網域名稱的使用者名稱](active-directory-add-domain-add-users.md)。如果您在確認網域名稱時遇到任何問題，請參閱我們的[疑難排解](#troubleshooting)一節。

## 疑難排解
如果無法確認自訂網域名稱，有幾個可能的原因。我們會從最常見的原因來開始逐一介紹到最不常見的原因。

- 您在傳播 DNS 項目之前就嘗試確認網域名稱。請稍待一會，然後再試一次。

- 您根本沒有輸入 DNS 記錄。請確認 DNS 項目並等它傳播，然後再試一次。

- 網域名稱已在其他目錄中進行確認。請找到該網域名稱並在其他目錄中加以刪除，然後再試一次。

- DNS 記錄含有錯誤。請更正錯誤，然後再試一次。

- 您的權限不足以更新 DNS 記錄。與組織中具有此存取權的人員或小組分享 DNS 項目，並請他們新增 DNS 項目。


## 後續步驟

-   [指派包含自訂網域名稱的使用者名稱](active-directory-add-domain-add-users.md)
-   [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
-   [了解 Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)
-   [在您的使用者登入時顯示公司的商標](active-directory-add-company-branding.md)
-   [使用 PowerShell 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->