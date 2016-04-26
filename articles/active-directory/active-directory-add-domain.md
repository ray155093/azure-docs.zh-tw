<properties
	pageTitle="新增自訂網域名稱以使用 Azure Active Directory 簡化登入作業 | Microsoft Azure"
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
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# 新增自訂網域名稱以使用 Azure Active Directory 簡化登入作業

您可以使用您自己的一或多個自訂網域名稱來改善及簡化 Azure Active Directory (Azure AD) 中的登入和其他使用者體驗。例如，如果您的組織擁有網域名稱 'contoso.com'，則您的使用者可利用熟悉的使用者名稱 (例如 'joe@contoso.com') 登入。 您必須能夠存取屬於 Azure AD 目錄全域管理員的使用者帳戶。

當您第一次取得 Azure Active Directory 中的租用戶目錄時，您的使用者會使用 ‘alice@contoso.onmicrosoft.com’ 之類的使用者名稱來登入。 在此範例中，contoso.onmicrosoft.com 是您在確認您的自訂網域名稱之前可以使用的內建初始網域名稱。您接下來要進行的其中一個步驟就是新增貴組織擁有的自訂網域名稱，例如 ‘contoso.com’。 這可讓您指派您的使用者熟悉的名稱，例如 ‘alice@contoso.com’。

如需有關如何在 Azure AD 中使用網域名稱的背景知識，請閱讀 [Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)。大部分的系統管理員會使用 Azure 傳統入口網站在 Azure AD 中執行網域名稱管理工作。不過如有需要，您也可以使用 [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或[圖形 API 預覽](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)來執行管理工作。

## 在目錄中新增自訂網域名稱

若要在目錄中新增自訂網域名稱：

1. 使用屬於 Azure AD目錄全域管理員的使用者帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2. 在左側的導覽列上選取 [Active Directory]。

3. 開啟您的目錄。

4. 選取 [網域] 索引標籤。

5. 在命令列上選取 [新增]。

6. 輸入您的自訂網域名稱，例如 ‘contoso.com’。請務必包含 .com、.net 或其他最上層的擴充。

7. 如果您打算對使用內部部署 Active Directory 的[同盟登入](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)設定此網域，請選取該核取方塊。

8. 選取 [新增]。

Azure AD 必須先確認您的組織擁有網域名稱，之後您才可以指派包含自訂網域名稱的使用者名稱。若要進行此確認動作，您必須在網域名稱註冊機構更新該網域的 DNS 項目。

## 取得 Azure AD 用來確認網域名稱的 DNS 項目

如果您已在新增網域時選取設定同盟網域的選項，您會看到 Azure AD Connect 工具的下載指引。請執行 Azure AD Connect 工具以便[取得您需要在網域名稱註冊機構新增的 DNS 項目](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

如果您未選取「使用 Windows Server AD 的同盟登入」的選項，您會在 [新增網域] 精靈的第二頁上看到 DNS 項目。

## 在網域的 DNS 區域檔案中新增 DNS 項目

若要新增 Azure AD 所需的 DNS 項目︰

1.  登入網域的網域名稱註冊機構。如果您的權限不足以更新網域的 DNS 區域檔案，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們幫忙更新。

2.  透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。這些 DNS 項目可讓 Azure AD 確認您擁有網域。它們不會變更任何行為，例如郵件路由或 Web 裝載。

[在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## 使用 Azure AD 確認網域

一旦您新增了 DNS 項目，您就可以使用 Azure AD 確認網域。

如果您選取了同盟自訂網域名稱的選項，Azure AD Connect 工具就會自動進行確認。完成必要條件之後，請執行此工具。否則，請在 Azure 傳統入口網站中確認網域。如果您的 [新增網域] 精靈仍保持開啟，您可以按一下精靈第三頁上的確認按鈕。傳播 DNS 記錄可能需要幾分鐘的時間。

如果 [新增網域] 精靈未保持開啟，您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中確認網域。

1.  使用屬於 Azure AD 目錄全域管理員的使用者帳戶進行登入。

2.  在左側的導覽列上選取 [Active Directory]。

3.  開啟您的目錄。

4.  選取 [網域] 索引標籤。

5.  在網域清單中，選取您想要確認的網域。

6.  在命令列上選取 [確認]。

7.  在對話方塊中選取 [確認]。

現在您可以指派包含自訂網域名稱的使用者名稱。

## 新增更多的自訂網域名稱

如果您的組織使用多個自訂網域名稱，例如 ‘contoso.com’ 和 ‘contosobank.com’，您可以在您的 Azure AD 目錄中新增每個自訂網域名稱，最多可新增 900 個網域。使用上述相同步驟新增後續的各個網域名稱。

後續步驟

-   [使用自訂網域名稱指派使用者名稱](active-directory-add-domain-add-users.md)

-   [管理自訂網域名稱](active-directory-add-manage-domain-names.md)

-   [在您的使用者登入時顯示公司的商標](active-directory-add-company-branding.md)

-   [Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->