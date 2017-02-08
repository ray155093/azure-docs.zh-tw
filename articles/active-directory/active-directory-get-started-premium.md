---
title: "開始使用 Azure Active Directory Premium"
description: "說明如何透過大量授權網站來註冊 Azure Active Directory Premium Edition 的主題。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7


---
# <a name="getting-started-with-azure-active-directory-premium"></a>開始使用 Azure Active Directory Premium
若要註冊 Active Directory Premium，您有數個選項︰ 

**Azure 或 Office 365** - 身為 Azure 或 Office 365 訂閱者，您可以線上購買 Active Directory Premium。 如需詳細步驟，請參閱[如何購買 Azure Active Directory Premium - 現有客戶](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer)或[如何購買 Azure Active Directory Premium - 新客戶](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)。  

**Enterprise Mobility + Security** - Enterprise Mobility + Security (先前的 Enterprise Mobility Suite) 是一個符合成本效益的方式，可供組織根據單一授權方案來搭配使用下列服務：Active Directory Premium、Azure Rights Management 和 Microsoft Intune。 如需詳細資訊，請參閱 [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) 網站。 若要取得 30 天的免費試用，請按一下 [這裡](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0)。

**Microsoft 大量授權** - Azure Active Directory Premium 可透過 [Microsoft Enterprise 合約](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 個以上的授權) 或 [Open 大量授權](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5-250 個授權) 方案來取得。

本主題說明如何開始使用您透過大量授權方案購買的 Azure Active Directory Premium。 如果您還不熟悉 Azure Active Directory 的不同版本，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。  

> [!NOTE]
> Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。 由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。 如需詳細資訊，請透過 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>步驟 1：註冊 Active Directory Premium
若要註冊，請參閱[如何透過大量授權來購買](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx)。

## <a name="step-2-activate-your-license-plan"></a>步驟 2：啟用您的授權方案
這是您透過 Microsoft 的企業大量授權方案所購買的第一個授權方案嗎？
若是如此，您會在購買完成時收到確認電子郵件。
您必須透過這封電子郵件才能啟用您的第一個授權方案。

後續針對此目錄的任何購買，授權將會自動在相同的目錄中啟用。

**若要啟用授權方案，請執行下列其中一個步驟︰**

1. 若要開始啟用，請按一下 [登入] 或 [註冊]。
   
    ![Sign in][1]

    - 如果您有現有的租用戶，請按一下 [登入]  以使用現有的系統管理員帳戶登入。 您必須使用目錄的全域管理員認證進行登入，且目錄必須已啟用授權。

    - 如果您想要建立新的 Azure Active Directory 租用戶來與您的授權方案搭配使用，請按一下 [註冊] 以開啟 [建立帳戶設定檔] 對話方塊。

        ![建立帳戶設定檔][2]

完成時將會顯示下列對話方塊，以確認您的租用戶的授權方案已啟用。

![確認][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>步驟 3：啟用 Azure Active Directory 存取
如果您之前已使用過 Microsoft Azure，您可以跳到 [步驟 4](#step-4-assign-license-to-user-accounts)。 

當有授權佈建到您的目錄時，您會收到 **歡迎電子郵件** 。 此電子郵件會確認您可以開始管理 Azure Active Directory Premium 或 Enterprise Mobility Suite 的授權和功能。 

如果您嘗試在收到歡迎電子郵件之前就先啟用您對 Azure Active Directory 的存取權，則會收到下列錯誤訊息。 

![無法存取][9]

如果您 請在收到電子郵件的幾分鐘後再試一次。

訂用帳戶中的新系統管理員也可透過此連結來啟用其對 Azure 傳統入口網站的存取權。

**若要啟用 Azure Active Directory 存取，請執行下列步驟：**

1. 在**歡迎電子郵件**中按一下 [登入]。 
   
    ![歡迎電子郵件][4]
2. 當您成功登入時，您必須完成形式為行動裝置驗證的第二因素驗證。
   
    ![行動裝置驗證][5]

啟用作業可能需要幾分鐘的時間。 您的存取權一旦啟用，棕色列便會消失，而您就可以按一下 [入口網站] 。

![設定中，請稍候][6]

在此案例中，您的 Azure 存取權僅限於 Azure Active Directory。

![Azure 功能][7]

在先前的使用中，您可能已經存取過 Azure。此外，您可以透過啟用其他 Azure 訂用帳戶，將 Access Azure Active Directory 升級為完整 Azure 存取。 在這些情況下，Azure 傳統入口網站會有更多功能。

![Azure 功能][8]

## <a name="step-4-assign-license-to-user-accounts"></a>步驟 4：將授權指派給使用者帳戶
在您可以開始使用所購買的方案之前，您必須以手動方式將授權指派給組織內的使用者帳戶，好讓他們可以使用 Premium 所提供的豐富功能。 使用下列步驟來將授權指派給使用者，好讓他們可以使用 Azure Active Directory Premium 功能。

**若要指派授權給使用者，請執行下列步驟：**

1. 以想要自訂之目錄的全域系統管理員身分登入 Azure 傳統入口網站。
2. 按一下 [ **Active Directory**]，然後選取您要指派授權的目錄。
3. 選取 [授權] 索引標籤，選取 [Active Directory Premium] 或 [Enterprise Mobility Suite]，然後按一下 [指派]。
   
    ![授權方案][10]
4. 在對話方塊中，選取您要對其指派授權的使用者，然後按一下核取記號圖示，以儲存變更。
   
    ![指派授權][11]

### <a name="license-restrictions"></a>授權限制
部分的授權方案屬於其他授權方案的子集或超集。 一般來說，您無法將授權方案重複指派給使用者。 如果您打算指派超集授權方案，則您必須先移除子集授權方案。

### <a name="license-requirements"></a>授權需求
當您將授權指派給使用者時，您可以在他們的帳戶屬性中指定一個主要使用位置。 如果未指定使用位置，則系統會自動將租用戶的位置指派給使用者。

![使用者位置][12]

Microsoft 雲端服務的服務和功能可用性會依國家或地區而有所不同。 服務 (例如網路電話，VoIP) 有可能在一個國家或地區可以使用，但在另一個國家或地區卻無法使用。 基於某些國家或地區的法律問題，服務中的功能可能會受到限制。 若要查看服務或功能在使用上有無限制，請到服務授權限制網站並尋找您的國家或區域。

## <a name="whats-next"></a>後續步驟
* [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

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



<!--HONumber=Dec16_HO2-->


