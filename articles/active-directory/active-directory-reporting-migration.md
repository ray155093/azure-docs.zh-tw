---
title: "如何在 Azure 入口網站中尋找活動報告 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中尋找活動報告"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 35323a56297c9aaf4939febd32140d6d101ee4e5
ms.openlocfilehash: 9cb98ad710d82bade25f99d171f49433aea1204c
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>如何在 Azure 入口網站中尋找活動報告

隨著從 Azure 傳統入口網站移轉到 Azure 入口網站，我們為您提供了新的「Azure Active Directory 活動記錄」風貌。 我們在幾個月前發表了一篇[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)，說明我們如何在您正在工作的資源內容中提供活動記錄。 本文將向您說明我們已如何將 Azure 傳統入口網站中現有的報告轉譯到 Azure 入口網站的全新世界中。

## <a name="what-is-new"></a>有什麼新功能？

Azure 傳統入口網站中的報告分成下列各種分類：

1.    安全性報告
2.    活動報告
3.    整合式應用程式報告

### <a name="activity-and-integrated-app-reports"></a>活動和整合式應用程式報告

我們已藉由移轉至 Azure 入口網站中的內容型報告功能，將現有的報告合併成一個單一檢視，其中是由單一基礎 API 將資料提供給該檢視。 

您可以在 [Azure Active Directory] 之 [活動] 區段中的 [稽核記錄] 之下找到此檢視。


![稽核記錄](./media/active-directory-reporting-migration/482.png "稽核記錄")








以下是已合併到此檢視中的報告。

-    稽核報告

-     密碼重設活動

-     密碼重設註冊活動

-     自助群組活動

-     Office365 群組名稱變更

-     帳戶佈建活動

-     密碼變換狀態
-     帳戶佈建錯誤


[應用程式使用量] 報告已增強並包含於名為 [登入] 的檢視中。 您可以在 [Azure Active Directory] 刀鋒視窗的 [活動] 區段中找到此檢視。


![稽核記錄](./media/active-directory-reporting-migration/483.png "稽核記錄")

此檢視包含所有使用者的登入，而後加以衍生即可取得應用程式使用量資訊。 您也可以透過 [企業應用程式] 概觀畫面來尋找應用程式使用量資訊。

![稽核記錄](./media/active-directory-reporting-migration/484.png "稽核記錄")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>我要如何存取此單一檢視中的特定報告？

### <a name="audit-logs"></a>稽核記錄

許多客戶會有的其中一個主要問題就是，是否能夠有多個篩選選項來存取 Azure AD 內的「活動記錄」。 作為替代方案，我們為您提供進階篩選機制來篩選您想要的資料。 我們提供的其中一個篩選稱為 [活動類別]，它會列出 Azure AD 所提供的各種活動記錄類型。 選擇您想要的類別，即可將結果縮小至您所尋找的範圍。 

例如，如果您只對取得「自助式密碼重設」相關的活動感興趣，您可以選擇 [自助式密碼管理] 分類。 您可看見的分類取決於您正在工作的資源。  


![稽核記錄](./media/active-directory-reporting-migration/06.png "稽核記錄")

 
目前我們擁有的各種分類如下：

- 核心目錄

- 自助式密碼管理

- 自助式群組管理

- 帳戶佈建

### <a name="application-usage"></a>應用程式使用情況

您可以透過 [活動] -> [登入] 檢視，檢視所有應用程式或單一應用程式的應用程式使用量。 如以下所式，不論是所有應用程式還是單一應用程式，都有此檢視。 如果您想要縮小結果範圍，您可以依據 [使用者名稱] 或 [應用程式名稱] 進行篩選。
 

![稽核記錄](./media/active-directory-reporting-migration/07.png "稽核記錄")


### <a name="security-reports"></a>安全性報告

來自 Azure 傳統入口網站的「Azure AD 異常活動」安全性報告已經合併，可為您提供一個 Azure Active Directory 能夠偵測及作為報告依據之所有安全性相關風險事件的集中檢視。 

下表列出 Azure 入口網站中的各種「Azure AD 異常活動」安全性報告，以及對應的風險事件類型。 

| Azure AD 異常活動報告 |  Identity Protection 風險事件類型|
| :--- | :--- |
| 認證外洩的使用者 | 認證外洩 |
| 異常的登入活動 | 不可能到達非典型位置的移動 |
| 從可能受感染的裝置登入 | 從受感染的裝置登入|
| 從不明來源登入 | 從匿名 IP 位址登入 |
| 從具有可疑活動的 IP 位址登入 | 從具有可疑活動的 IP 位址登入 |
| - | 從不熟悉的位置登入 |

下列 Azure AD 異常活動安全性報告並未包含在 Azure AD 的風險事件中。 這些報告仍可在 Azure 傳統入口網站中取得。 不過，未來會將它們淘汰。

* 在多次失敗後登入
* 從多個地理區域登入

如需完整概觀，請參閱 [Azure Active Directory 風險事件](active-directory-identity-protection-risk-events.md)。  

在 Azure 入口網站中，您可以在 [Azure Active Directory] 刀鋒視窗的 [安全性] 區段中，存取所偵測到之風險事件的相關報告。 偵測到的風險事件會在下列報告中進行追蹤︰   

- 有風險的使用者
- 有風險的登入 


![稽核記錄](./media/active-directory-reporting-migration/04.png "稽核記錄")

如需這些報告的詳細資訊，請參閱：

- [Azure Active Directory 入口網站 (預覽版) 中有風險的使用者安全性報告](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 入口網站 (預覽版) 中有風險的登入報告](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure 傳統入口網站與 Azure 入口網站中之活動報告的比較

本節會列出 Azure 傳統入口網站中現有的報告，以及您如何在 Azure 入口網站中取得此資訊。


所有稽核資料的進入點是 [Azure Active Directory] 刀鋒視窗的 [活動] 區段中的 [稽核記錄]。

![稽核記錄](./media/active-directory-reporting-migration/61.png "稽核記錄")


| Azure 傳統入口網站                 | Azure 入口網站步驟                                                         |
| ---                                  | ---                                                                        |
| 稽核記錄                           | 選取 [核心目錄] 做為 [活動類別]。                       |
| 密碼重設活動              | 選取 [自助式密碼管理] 做為 [活動類別]。 | 
| 密碼重設註冊活動 | 選取 [自助式密碼管理] 做為 [活動分類]。     |
| 自助群組活動         | 選取 [自助式群組管理] 做為 [活動類別]。        |
| 帳戶佈建活動        | 選取 [帳戶使用者佈建] 做為 [活動類別]。         |
| 密碼變換狀態             | 選取 [自動應用程式密碼變換] 做為 [活動類別]。      |
| 帳戶佈建錯誤          | 選取 [帳戶使用者佈建] 做為 [活動類別]。        |
| Office365 群組名稱變更         | 選取 [自助式密碼管理] 做為[活動類別]，選取 [群組] 做為[活動資源類型]，以及選取 [O365 群組] 做為 [活動來源]。|

 

[應用程式使用量] 報告的進入點是 [Azure Active Directory] > [企業應用程式] > [登入]。 


![稽核記錄](./media/active-directory-reporting-migration/199.png "稽核記錄")


