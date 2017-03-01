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
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>如何在 Azure 入口網站中尋找活動報告

隨著從 Azure 傳統入口網站移轉到 Azure 入口網站，我們為您提供了新的「Azure Active Directory 活動記錄」風貌。 我們在幾個月前發表了一篇[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)，說明我們如何在您正在工作的資源內容中提供活動記錄。 本文將向您說明我們已如何將 Azure 傳統入口網站中現有的報告轉譯到 Azure 入口網站的全新世界中。

## <a name="what-is-new"></a>有什麼新功能？

Azure 傳統入口網站中的報告分成下列各種分類：

1.    安全性報告
2.    活動報告
3.    整合式應用程式報告

### <a name="activity-and-integrated-app-reports"></a>活動和整合式應用程式報告

我們已藉由移轉至 Azure 入口網站中的內容型報告功能，將現有的報告合併成一個單一檢視，其中是由單一基礎 API 將資料提供給該檢視。 您可以在 Azure 入口網站中 (左側瀏覽區) 的 [活動] > [稽核記錄檔] 底下，找到這個檢視。 以下是已合併到此檢視中的報告。

-    稽核報告

-     密碼重設活動

-     密碼重設註冊活動

-     自助群組活動

-     Office365 群組名稱變更

-     帳戶佈建活動

-     密碼變換狀態
-     帳戶佈建錯誤


「應用程式使用量」報告已經增強而包含在名為 [活動] -> [登入] (左側導覽區) 的檢視中，其中包含所有使用者的登入，加以衍生後，即可取得「應用程式使用量」資訊。 您也可以透過 [企業應用程式] 概觀畫面來尋找應用程式使用量資訊。

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>我要如何存取此單一檢視中的特定報告？

### <a name="audit-logs"></a>稽核記錄檔

許多客戶會有的其中一個主要問題就是，是否能夠有多個篩選選項來存取 Azure AD 內的「活動記錄」。 作為替代方案，我們為您提供進階篩選機制來篩選您想要的資料。 我們提供的其中一個篩選稱為 [活動分類]，它會列出 Azure AD 所提供的各種不同類型「活動記錄」。 您只要選擇想要的分類，即可將「活動記錄」的結果範圍縮小到您有興趣檢視的結果。 

例如，如果您只對取得「自助密碼重設」相關的活動感興趣，您可以選擇 [自助密碼管理] 分類。 您可看見的分類取決於您正在工作的資源。  


![稽核記錄檔](./media/active-directory-reporting-migration/06.png "稽核記錄檔")

 
目前我們擁有的各種分類如下：

- 核心目錄

- 自助密碼管理

- 自助式群組管理

- 帳戶佈建

### <a name="application-usage"></a>應用程式使用情況

您可以透過 [活動] -> [登入] 檢視，來檢視所有應用程式或單一應用程式的「應用程式使用量」。 如以下所式，不論是所有應用程式還是單一應用程式，都有此檢視。 如果您想要縮小結果範圍，您可以依據「使用者名稱」或「應用程式名稱」進行篩選。
 

![稽核記錄檔](./media/active-directory-reporting-migration/07.png "稽核記錄檔")


### <a name="security-reports"></a>安全性報告

安全性報告已經合併，可提供 Azure Active Directory 能夠偵測及作為報告依據的所有風險訊號檢視。

這個合併的檢視可提供您下列相關資料：

- 有風險的使用者
- 有風險的登入 


![稽核記錄檔](./media/active-directory-reporting-migration/04.png "稽核記錄檔")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure 傳統入口網站與 Azure 入口網站中之活動報告的比較

本節會列出 Azure 傳統入口網站中現有的報告，以及您如何在 Azure 入口網站中取得此資訊。

**稽核記錄檔：**

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [核心目錄] 

**密碼重設活動：**

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [自助密碼管理] 

**密碼重設登錄活動：**    

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [自助密碼管理]。

**自助式群組活動：**    

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [自助式群組管理]

**Office365 群組名稱變更：**

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [自助密碼管理]
3. 在 [活動資源類型]，選取 [群組] 
4. 在 [活動來源]，選取 [O365 群組] 

**帳戶佈建活動：**    

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [帳戶使用者佈建]

**密碼變換狀態：**    

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [自動應用程式密碼變換]

**帳戶佈建錯誤：**

1. 在左側瀏覽窗格中，按一下 [活動]，然後按一下 [稽核記錄檔]
2. 在 [活動分類]，選取 [帳戶使用者佈建]

**應用程式使用量：**

- 在左側瀏覽窗格中，按一下 [企業應用程式]，然後按一下 [登入]



