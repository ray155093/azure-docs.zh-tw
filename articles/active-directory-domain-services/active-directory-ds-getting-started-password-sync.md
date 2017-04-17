---
title: "Azure Active Directory Domain Services︰啟用密碼同步處理 | Microsoft Docs"
description: "開始使用 Azure Active Directory 網域服務"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6ed35ca1b83f5f7221824d99817800c1e42e68c1
ms.lasthandoff: 04/12/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 啟用密碼同步處理
在先前工作中，您已啟用 Azure Active Directory (Azure AD) 租用戶的 Azure Active Directory Domain Services (AD DS)。 下一項工作是啟用 NT LAN Manager (NTLM) 和 Kerberos 驗證所需的認證雜湊，以同步處理到 Azure Active Directory Domain Services。 設定認證同步處理後，使用者即可使用他們的公司認證來登入受管理的網域。

程序會根據組織有僅限雲端的 Azure AD 租用戶，或已設定為使用 Azure AD Connect 來與內部部署目錄同步處理而有所不同。

> [!div class="op_single_selector"]
> * [僅限雲端的 Azure AD 租用戶](active-directory-ds-getting-started-password-sync.md)
> * [已同步處理的 Azure AD 租用戶](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>工作 5︰為僅限雲端的 Azure AD 租用戶啟用 Azure Active Directory Domain Services 的密碼同步處理
若要在受管理的網域上驗證使用者，Azure Active Directory Domain Services 需要適合於 NTLM 和 Kerberos 驗證之格式的認證雜湊。 除非您針對租用戶啟用 Azure Active Directory Domain Services，否則 Azure AD 不會以 Kerberos 和 NTLM 驗證所需的格式產生或儲存認證雜湊。 基於明顯的安全性考量，Azure AD 也不會儲存任何純文字形式的認證。 因此，Azure AD 沒辦法根據使用者的現有認證來產生這些 NTLM 或 Kerberos 認證雜湊。

> [!NOTE]
> 如果您的組織有僅限雲端的 Azure AD 租用戶，則需要使用 Azure Active Directory Domain Services 的使用者必須變更其密碼。
>
>

此密碼變更程序會在 Azure AD 中產生 Azure Active Directory Domain Services 進行 Kerberos 和 NTLM 驗證所需的認證雜湊。 您可以針對租用戶中所有需要使用 Azure Active Directory Domain Services 的使用者來使其密碼過期，或者指示他們變更其密碼。

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>為僅限雲端的 Azure AD 租用戶啟用 NTLM 和 Kerberos 認證雜湊產生功能
以下是您需要提供使用者才能變更其密碼的指示：

1. 移至貴組織的 [Azure AD 存取面板][](http://myapps.microsoft.com) 頁面。
2. 在 [存取面板] 視窗中，選取 [設定檔] 索引標籤。
3. 按一下 [變更密碼] 圖格。

    ![Azure AD 存取面板「變更密碼」圖格](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > 如果在 [存取面板] 視窗中未顯示 [變更密碼] 選項，請確定您的組織已設定 [Azure AD 中的密碼管理](../active-directory/active-directory-passwords-getting-started.md)。
   >
   >
4. 在 [變更密碼] 頁面上，輸入您現有 (舊的) 密碼，然後輸入新密碼並加以確認。 

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. 按一下 [提交] 。

當您變更密碼幾分鐘之後，新的密碼就能在 Azure Active Directory Domain Services 中使用。 再幾分鐘 (通常大約 20 分鐘) 之後，您就能使用最近變更的密碼來登入已加入受管理網域的電腦。

## <a name="next-steps"></a>後續步驟
* [如何更新自己的密碼](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [在 Azure AD 中開始使用密碼管理](../active-directory/active-directory-passwords-getting-started.md)
* [為同步處理的 Azure AD 租用戶啟用 Azure Active Directory Domain Services 的密碼同步處理](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [受 Azure Active Directory Domain Services 管理的網域](active-directory-ds-admin-guide-administer-domain.md)
* [將 Windows 虛擬機器加入 Azure Active Directory Domain Services 受管理的網域](active-directory-ds-admin-guide-join-windows-vm.md)
* [將 Red Hat Enterprise Linux 虛擬機器加入 Azure Active Directory Domain Services 受管理的網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

