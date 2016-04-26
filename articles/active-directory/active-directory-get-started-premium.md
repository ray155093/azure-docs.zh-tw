<properties
	pageTitle="開始使用 Azure Active Directory Premium"
	description="說明如何註冊 Azure Active Directory Premium Edition 的主題。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="markvi"/>

# 開始使用 Azure Active Directory Premium

Azure Active Directory 有三種版本：免費、基本和進階。免費版本隨附於 Azure 或 Office 365 訂用帳戶。基本和進階版本可透過 [Microsoft Enterprise 合約](https://www.microsoft.com/zh-TW/licensing/licensing-programs/enterprise.aspx)或[開啟大量授權](https://www.microsoft.com/zh-TW/licensing/licensing-programs/open-license.aspx) 程式取得。Azure 和 Office 365 訂閱者也可以線上購買 Active Directory Premium。[在此登入](https://portal.office.com/Commerce/Catalog.aspx)購買。

> [AZURE.NOTE]
Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。如需詳細資訊，請透過 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。

Azure Active Directory Premium 也包含在 **Enterprise Mobility Suite** 中。Enterprise Mobility Suite 提供符合成本效益的方式供組織在一個授權方案下一起使用下列服務︰

- Active Directory Premium 
- Azure Rights Management
- Microsoft Intune


如需詳細資訊，請參閱 [Enterprise Mobility Suite](https://www.microsoft.com/zh-TW/server-cloud/enterprise-mobility/overview.aspx) 網站。

若要立即開始使用 Azure Active Directory Premium 功能，請遵照後續幾節的步驟進行。這些步驟也確實適用於 Azure Active Directory Basic 版本。

## 步驟 1：註冊 Active Directory Premium

若要註冊，請參閱[大量授權](http://www.microsoft.com/zh-TW/licensing/how-to-buy/how-to-buy.aspx)網站。

## 步驟 2：啟用您的授權方案

當您透過 Microsoft 的企業大量授權方案購買了第一個授權方案時，您會收到確認電子郵件。您必須透過這封電子郵件才能啟用您的第一個授權方案。後續針對此目錄的任何購買，授權將會自動在相同的目錄中啟用。

若要開始啟用，請按一下 [登入] 或 [註冊]。


![][1]

如果您有現有的租用戶，請按一下 [登入] 以使用現有的系統管理員帳戶登入。請務必使用目錄的全域管理員認證進行登入，且目錄必須已啟用授權。

如果您想要建立新的 Azure Active Directory 租用戶來與您的授權方案搭配使用，請按一下 [註冊] 以開啟 [建立帳戶設定檔] 對話方塊。

![][2]

完成時將會顯示下列對話方塊，以確認您的租用戶的授權方案已啟用。

![][3]

## 步驟 3：啟用 Azure Active Directory 存取

當有授權佈建到您的目錄時，您會收到**歡迎電子郵件**。此電子郵件會確認您可以開始管理 Azure Active Directory Premium 或 Enterprise Mobility Suite 的授權和功能。

如果您之前用過 Microsoft Azure，您可以前往 [http://manage.windowsazure.com](http://manage.windowsazure.com) 以指派新的授權 (如需詳細資訊，請參閱[步驟 4](#step-4-assign-license-to-user-accounts))。

如果您不曾用過 Microsoft azure，您可以按一下電子郵件中的 [登入] 或移至[存取 Azure Active Directory 啟用頁面](https://account.windowsazure.com/signup?offer=MS-AZR-0110P)。這兩種方法都會帶您完成一系列的步驟，協助您透過 Azure 傳統入口網站存取您的目錄。

![][4]

當您成功登入時，您必須提供行動電話號碼並加以驗證，才能完成第二因素驗證畫面。當您完成行動驗證時，您就可以按一下 [註冊] 來啟用您對 Azure Active Directory 的存取權。

![][5]

啟用作業可能需要幾分鐘的時間。您的存取權一旦啟用，棕色列便會消失，而您就可以按一下 [入口網站]。

![][6]

在此案例中，您的 Azure 存取權僅限於 Azure Active Directory。

![][7]

在先前的使用中，您可能已經存取過 Azure。此外，您可以透過啟用其他 Azure 訂用帳戶，將 Access Azure Active Directory 升級為完整 Azure 存取。在這些情況下，Azure 傳統入口網站會有更多功能。

![][8]

如果您嘗試在收到歡迎電子郵件之前就先啟用您對 Azure Active Directory 的存取權，則有可能會看到下列錯誤訊息。請在收到電子郵件的幾分鐘後再試一次。

![][9]

訂用帳戶中的新系統管理員也可透過此連結來啟用其對 Azure 傳統入口網站的存取權。

## 步驟 4：將授權指派給使用者帳戶

在您可以開始使用所購買的方案之前，您必須以手動方式將授權指派給組織內的使用者帳戶，好讓他們可以使用 Premium 所提供的豐富功能。使用下列步驟來將授權指派給使用者，好讓他們可以使用 Azure Active Directory Premium 功能。

將授權指派給使用者：

1. 以想要自訂之目錄的全域系統管理員身分登入 Azure 傳統入口網站。
2. 按一下 [Active Directory]，然後選取您要指派授權的目錄。
3. 選取 [授權] 索引標籤，選取 [Active Directory Premium] 或 [Enterprise Mobility Suite]，然後按一下 [指派]。

    ![][10]

4. 在對話方塊中，選取您要對其指派授權的使用者，然後按一下核取記號圖示，以儲存變更。

    ![][11]

## 授權限制

部分的授權方案屬於其他授權方案的子集或超集。一般來說，您無法將授權方案重複指派給使用者。如果您打算指派超集授權方案，則您必須先移除子集授權方案。

## 授權需求

當您將授權指派給使用者時，您可以在他們的帳戶屬性中指定一個主要使用位置。如果未指定使用位置，則系統會自動將租用戶的位置指派給使用者。

![][12]

Microsoft 雲端服務的服務和功能可用性會依國家或地區而有所不同。服務 (例如網路電話，VoIP) 有可能在一個國家或地區可以使用，但在另一個國家或地區卻無法使用。基於某些國家或地區的法律問題，服務中的功能可能會受到限制。若要查看服務或功能在使用上有無限制，請到服務授權限制網站並尋找您的國家或區域。

## 後續步驟

- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=AcomDC_0420_2016-->