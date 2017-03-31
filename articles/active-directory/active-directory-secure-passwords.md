---
title: "在 Azure AD 中保護密碼及重設遭到智慧型密碼鎖定封鎖的密碼 | Microsoft Docs"
description: "說明 Azure AD 租用戶是什麼，以及如何透過 Azure Active Directory 管理 Azure"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>在 Azure AD 中保護密碼及重設遭到智慧型密碼鎖定封鎖的密碼
本文討論身為使用者或系統管理員的您可以遵循的最佳做法，以便保護您的 Azure Active Directory (Azure AD) 和 Microsoft 帳戶服務帳戶。 

 >[!NOTE]
 >Azure AD 系統管理員可以按一下目錄名稱，以重設使用者密碼。 從 [Azure 管理入口網站](https://manage.windowsazure.com)，選擇 [使用者] 頁面，按一下使用者的名稱和 [重設密碼]。 
 >

Azure AD 併入下列一般方法來保護密碼︰
 *    密碼長度需求
 *    密碼「複雜性」需求
 *    一般和定期密碼到期 

如需密碼管理功能的相關資訊，請參閱[在 Azure Active Directory 中管理密碼](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords)。 

## <a name="azure-ad-password-protection"></a>Azure AD 密碼保護
Azure AD 和 Microsoft 帳戶系統使用業界證實可行的方法，以確保使用者和系統管理員密碼的保護。 

本節討論 Azure AD 如何使用下列方法來保護密碼︰
 *    動態禁用的密碼
 *    智慧型密碼鎖定

如需以目前研究為基礎的密碼管理相關資訊，請參閱[密碼指引](http://aka.ms/passwordguidance)白皮書。 

### <a name="dynamically-banned-passwords"></a>動態禁用的密碼
Azure AD 和 Microsoft 帳戶系統藉由動態禁用所有常用的密碼來提供密碼保護。 Azure ID Identity Protection 小組會定期分析遭到禁用的密碼清單，並防止使用者選取常用的密碼。 此服務適用於 Azure AD 和 Microsoft 帳戶服務客戶。 

建立密碼時，系統管理員務必鼓勵使用者選擇不常見的密碼片語，其中包含字母、數字和字元的唯一組合。 這有助於讓使用者密碼幾乎不可能遭到入侵。 

**外洩清單**

Azure AD 一直努力在網路罪犯發生前加以防範。 其中一種做法是防止使用者建立目前攻擊清單上的密碼。

Azure AD Identity Protection 小組會持續分析常用的密碼。 網路罪犯也會使用類似的策略來告知其攻擊，例如建立[彩虹表](https://en.wikipedia.org/wiki/Rainbow_table)用來破解密碼雜湊。 

Microsoft 會持續分析[資料外洩](https://www.privacyrights.org/data-breaches)，以維護動態更新的禁用密碼清單，進而確保易受攻擊的密碼在成為 Azure AD 客戶的真正威脅之前遭到禁用。 如需目前安全性成果的詳細資訊，請參閱 [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx)。 

### <a name="smart-password-lockout"></a>智慧型密碼鎖定

當 Azure AD 偵測到潛在網路罪犯嘗試入侵使用者密碼時，我們會使用智慧密碼鎖定來鎖定使用者帳戶。 Azure AD 設計用來判斷與特定登入工作階段相關聯的風險。 

我們會使用最新的安全性資料，將鎖定語意套用至網路威脅。 如此一來，在網路罪犯入侵您網路上使用者密碼的情況下，使用者就不會遭到鎖定。

如果使用者的 Azure AD 遭到鎖定，其畫面如下所示︰

  ![Azure AD 遭到鎖定](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
對於其他 Microsoft 帳戶，其畫面如下所示︰

  ![Microsoft 帳戶遭到鎖定](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

如需 Azure Active Directory 中密碼管理的相關資訊，請參閱[密碼管理運作方式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works)。

  >![NOTE] 如果您是 Azure AD 系統管理員，可以使用 [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) 全然避免使用者建立傳統密碼。
  >

## <a name="next-steps"></a>後續步驟
[如何更新自己的密碼](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Azure 身分識別管理的基本概念](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[如何使用密碼管理報告取得 Operational Insights](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



