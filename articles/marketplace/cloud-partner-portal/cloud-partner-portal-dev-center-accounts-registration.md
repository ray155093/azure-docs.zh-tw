---
title: "如何建立用於發佈 Azure Marketplace 的 Microsoft 開發人員帳戶 | Microsoft Docs"
description: "這篇文章說明建立 Azure Marketplace 與發佈所適用 Microsoft 開發人員帳戶的步驟。"
services: cloud-partner-portal
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: edad5a8a2acb2767dc59b51429ae3fc581f74947
ms.lasthandoff: 04/18/2017


---
# <a name="create-a-microsoft-developer-account"></a>建立 Microsoft 開發人員帳戶
本文將逐步引導您完成要成為可發佈 Azure Marketplace 的核准 Microsoft 開發人員所必須執行的帳戶建立和註冊程序。

## <a name="1-create-a-microsoft-account"></a>1.建立 Microsoft 帳戶
若要啟動發佈程序，您必須先完成 **Microsoft 開發人員中心**的註冊。您將在 **[Cloud Partner 入口網站](https://cloudpartner.azure.com/)**上使用同一個註冊的帳戶來啟動發佈程序。 您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。 其不應該特別屬於服務或優惠。

構成使用者名稱的電子郵件地址應該位於您的網域中，並由您的 IT 小組所控制。 所有和發佈相關的活動都應透過此帳戶完成。

> [!WARNING]
> Microsoft 帳戶註冊不支援使用 **Azure** 和 **Microsoft** 之類的單字。 請避免使用這些字，以完成帳戶建立及註冊程序。
>
>

### <a name="guidelines-for-company-accounts"></a>公司帳戶的指導方針
建立公司帳戶時，如果有多人需要以開啟該帳戶的 Microsoft 帳戶進行登入來存取該帳戶，請遵循下列指導方針。

> [!Important]
> 重要事項：若要允許多個使用者存取您的開發人員中心帳戶，建議使用 Azure Active Directory 將角色指派給個別使用者，該使用者可使用其個別 Azure AD 認證進行登入以存取該帳戶。 如需詳細資訊，請參閱[管理帳戶使用者](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users)。

* 使用屬於貴公司網域，但不屬於單一個人的電子郵件地址，建立您的 Microsoft 帳戶 — 例如，windowsapps@fabrikam.com。
* 將此 Microsoft 帳戶的存取限制為可能最少的開發人員。
* 設定公司電子郵件通訊群組清單，其中包含每一位需要存取開發人員帳戶的使用者，並將此電子郵件地址新增至您的安全性資訊。 這可讓清單上的所有員工在必要時接收安全碼，以及管理您的 Microsoft 帳戶安全性資訊。 如果設定通訊群組清單不可行，則個人電子郵件帳戶的擁有者必須能在出現提示時存取及共用安全碼 (例如將新的安全性資訊新增至帳戶時，或是必須從新裝置存取安全性資訊時)。
* 新增重要小組成員可以存取的公司電話號碼 (不需要分機號碼)。
* 一般而言，讓程式開發人員使用受信任的裝置來登入公司的開發人員帳戶。 所有重要小組成員應該可以存取這些受信任的裝置。 這會降低存取帳戶時傳送安全碼的需求。
* 如果您需要允許從非信任的電腦帳戶存取，請將該存取限制為最多五個開發人員。 在理想情況下，這些開發人員應該從共用相同地理和網路位置的電腦存取該帳戶。
* 在 [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) 經常檢閱貴公司的安全性資訊，以確保全都是最新資訊。

您的開發人員帳戶主要應從受信任的電腦存取。 這點很重要，因為每個帳戶每週產生的安全碼數目有所限制。 此外，也可提供最順暢的登入體驗。

如需其他開發人員帳戶指導方針和安全性的詳細資訊，請按一下[這裡](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)。

### <a name="instructions"></a>範例的指示
1. 開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入現有帳戶。
2. 使用 [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) 連結，將電子郵件 (如上述指導方針的 windowsapp@fabrikam.com) 註冊為 Microsoft 帳戶。 請遵循下列指示：

       A. During registering your account as a Microsoft account, you need to provide a valid phone number for the system to send you an account verification code as a text message or an automated call.

       B. During registering your account as a Microsoft account, you need to provide a valid email id for receiving an automated email for account verification.

    C. 請確認傳送至 DL 的電子郵件地址。

    D. 您現在可以在「Microsoft 開發人員中心」中開始使用新的 Microsoft 帳戶。

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2.在 Microsoft 開發人員中心註冊您的帳戶

Microsoft 開發人員中心用於註冊一次公司資訊。 註冊者必須是公司的有效代表，而且必須提供個人資訊用來驗證其身分識別。 註冊的人員必須使用公司內共用的 Microsoft 帳戶，**而且在 Cloud Partner 入口網站中必須使用同一個帳戶。** 當您嘗試建立帳戶之前，應該先檢查公司是否確實未曾擁有 Microsoft 開發人員中心帳戶。 在這個程序期間，我們會收集公司地址資訊、銀行帳戶資訊和稅務資訊。 這些資訊通常可以從金融或商務連絡人處取得。

> [!IMPORTANT]
> 您必須完成下列開發人員設定檔元件，才能進行優惠建立和部署程序的各個階段。
>
>

| 開發人員設定檔 | 開始草擬 | 預備 | 免費發佈與解決方案範本 | 商業發佈 |
| --- | --- | --- | --- | --- |
| 公司註冊 |必備 |必備 |必備 |必備 |
| 稅務設定檔識別碼 |選用 |選用 |選用 |必備 |
| 銀行帳戶 |選用 |選用 |選用 |必備 |

> [!NOTE]
> 虛擬機器僅支援自備授權 (BYOL)，並將它視為 **免費** 供應項目。
>
>

### <a name="register-your-company-account"></a>註冊您的公司帳戶
步驟 1. 開啟新的 Internet Explorer InPrivate 或 Chrome Incognito 瀏覽工作階段，確定您未登入個人帳戶。

步驟 2. 移至 [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) ，將自己註冊為開發人員中心的賣方。 在您繼續之前，請先閱讀下列重要事項。

![繪圖][img-verify]

   > [!IMPORTANT]
   > 確定將在開發人員中心註冊的電子郵件識別碼或通訊群組清單 (為了不受限於個人使用，建議使用通訊群組清單) 已註冊為 Microsoft 帳戶。 如果還未註冊為 Microsoft 帳戶，請使用此 [連結](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1)註冊。 此外，**不得使用隸屬於 Microsoft 公司網域 (亦即 @microsoft.com) 的任何電子郵件識別碼**在開發人員中心註冊。
   >
   >

![開發人員中心登入](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

步驟 3. 請完成 [協助我們保護您的帳戶] 精靈，它會透過電話號碼或電子郵件地址驗證您的身分識別。

步驟 4. 在 [註冊帳戶資訊] 區段中，從下拉式功能表選取您的 [帳戶國家/區域]  。

 ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **「銷售來源」國家/地區：** 如果要在 Azure Marketplace 上銷售服務，您的註冊實體必須是其中一個經過核准的「銷售來源」國家/地區。 這項限制是基於付款和稅務理由。 我們正積極在不久的將來擴充這份國家/地區清單，所以請密切注意。 如需詳細資訊，請參閱 [Marketplace 參與原則](http://go.microsoft.com/fwlink/?LinkID=526833)。
   >


步驟 5。 在 [帳戶類型] 選取 [公司]，然後按一下 [下一步] 按鈕。

   > [!IMPORTANT]
   > 若要進一步了解帳戶類型，以及何者最適合您的選擇，請檢視頁面 [帳戶類型、位置和費用](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

   ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

步驟 6. 輸入 [發佈者顯示名稱] ，通常是您公司的名稱。

   > [!TIP]
   > 您的供應項目列出後，在開發人員中心輸入的發行者顯示名稱並不會顯示在 Azure Marketplace。 但是，必須填寫這項資訊以完成註冊程序。
   >


步驟 7. 輸入 **連絡資訊** 以進行帳戶驗證。

   > [!IMPORTANT]
   > 您必須提供精確的連絡資訊，因為它將用於我們的驗證程序，讓您的公司在開發人員中心獲得核准。
   >


步驟 8。 輸入 **公司核准者**的連絡資訊。 公司核准者是可以驗證您有權代表您的組織在開發人員中心建立帳戶的人員。 完成時，按一下 [下一步] 以移至 [付款] 區段。

   ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

步驟 9. 輸入您的付款資訊以支付您的帳戶。 如果您有涵蓋註冊成本的促銷代碼，您可以在此處輸入。 否則，請提供您的信用卡資訊 (或受支援市場的 PayPal)。 當您完成時，按一下 [下一步] 以移至 [檢閱] 畫面。

  ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

步驟 10. 檢閱您的帳戶資訊，並確認所有項目都正確。 然後，閱讀並接受「Microsoft Azure Marketplace 發佈者合約」 [](http://go.microsoft.com/fwlink/?LinkID=699560)的條款和條件。 勾選方塊以表示您已閱讀並接受這些條款。

步驟 11. 按一下 [完成]  以確認您的註冊。 我們會傳送一則確認訊息到您的電子郵件地址。

步驟 12. 如果您打算只發佈免費供應項目，請按一下 [移至 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)]。 您可以跳到本文件的第 3 節。

如果您打算發佈商業供應項目 (例如採每小時計費模式的虛擬機器供應項目)，按一下 [更新您的帳戶資訊]  ，您必須在該處填寫開發人員中心帳戶的稅務和銀行資訊。

如果您想要稍後再更新您的稅金和銀行資訊，則可以移到下一節，也就是這份文件的第 3 節。

> [!IMPORTANT]
> 對於商業供應項目，如果未填寫稅務和銀行帳戶資訊，則您無法將供應項目推送到生產環境。
>
>

### <a name="add-tax-and-banking-information"></a>新增稅務和銀行資訊
 如果您想要發佈商業供應項目供採購，則還需要新增付款和稅務資訊，然後在開發人員中心中提交以供驗證。 如果您只要發佈免費供應項目 (或 BYOL 供應項目)，則不需要加入這項資訊。 您可以稍後再新增，但請花一些時間驗證稅務資訊。 如果您知道將會提供商業供應項目供採購，建議您儘快新增。

**銀行資訊**

1. 使用您的 Microsoft 帳戶登入 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) 。
2. 按一下左側功能表中的 [支付帳戶]，在 [選擇付款方式] 下，按一下 [銀行帳戶] 或 [PayPal]。

   > [!IMPORTANT]
   > 如果您商業供應項目可供客戶在 Marketplace 購買，則這是您將收到購物付款金額的帳戶。
   >
   >
3. 輸入付款資訊，然後在滿意時按一下 [儲存]  。

   > [!IMPORTANT]
   > 如果您需要更新或變更您的付款帳戶，請遵循上述的相同步驟，以新資訊取代目前的資訊。 變更您的付款帳戶會延遲您的付款最多一次付款週期。 會發生此延遲是因為我們必須確認帳戶變更，就像我們在您第一次設定付款帳戶時所做的一樣。 您仍然會在帳戶確認之後收到全額款項。當期付款週期未付的款項會新增至下一期。
   >
   >
4. 按 [下一步] 。

**稅務資訊**

1. 使用您的 Microsoft 帳戶登入 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) (視需要)。
2. 按一下左側功能表中的 [稅務設定檔]  。
3. 在 [設定您的稅單]  頁面上，選取您的永久居住國家或區域，並選取您擁有主要國籍的國家或區域。 按 [下一步] 。
4. 輸入稅務詳細資料，然後按 [下一步] 。

> [!WARNING]
> 如果沒有在 Microsoft 開發人員中心帳戶中填寫稅務和銀行帳戶資訊，則您無法將商業供應項目推送到生產環境。
>
>

如果在開發人員中心註冊時發生問題，請記錄支援票證，如下所示：

1. 移至支援連結 [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. 在 [與我們連絡] 區段中，按一下 [提交事件] 按鈕 (如以下螢幕擷取畫面所示)

  ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3. 針對 [問題類型] 選擇 [開發人員中心協助]，針對 [類別] 選擇 [發行和管理應用程式]。 然後按一下 [開始撰寫電子郵件] 按鈕。

  ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)
4. 您將被導向登入頁面。 使用任一 Microsoft 帳戶登入。 如果您沒有 Microsoft 帳戶，請使用此 [連結](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)建立一個。
5. 填入問題的詳細資訊，然後按一下 [提交]  按鈕以提交票證。

  ![繪圖](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-cloud-partner-portal"></a>3.在 Cloud Partner 入口網站中註冊您的帳戶
[Cloud Partner 入口網站](https://cloudpartner.azure.com/)可用來發佈和管理您的供應項目。

1. 開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入個人帳戶。
2. 移至 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
3. 如果您是第一次登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)的新使用者，您必須使用註冊開發人員中心帳戶所用的同一個電子郵件識別碼登入。 如此一來，您的開發人員中心帳戶和 Cloud Partner 入口網站帳戶就會彼此連結。 您之後可以遵循下列步驟，將公司中使用應用程式的其他成員新增成為 Cloud Partner 入口網站中的參與者或擁有者。

如果已將您新增成為 Cloud Partner 入口網站中的參與者/擁有者，您便可以使用自己的帳戶登入。

> [!TIP]
> 如需參與原則的說明，請參閱 [Azure 網站](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。
>
>

## <a name="4-steps-to-manage-users-as-owners-or-contributor-in-the-cloud-partner-portal"></a>4.以 Cloud Partner 入口網站中擁有者或參與者角色管理使用者的步驟

[在 Cloud Partner 入口網站上管理使用者的步驟](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>後續步驟
現在，您的帳戶已建立並註冊完成，您可以啟動 Azure Marketplace 發佈程序。

[img-msalive]:media/cloud-partner-portal-create-dev-center-registration/creating-msa-account-msa-live.jpg
[img-email]:media/cloud-partner-portal-create-dev-center-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-incognito.jpg
[img-signin]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg
[img-verify]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal.jpg

[img-sd-type]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-acc-type.jpg

[img-sd-mktg1]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-approval.jpg


[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
