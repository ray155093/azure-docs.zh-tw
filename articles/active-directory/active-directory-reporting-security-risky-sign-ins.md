---
title: "Azure Active Directory 入口網站中有風險的登入報告 | Microsoft Docs"
description: "了解 Azure Active Directory 入口網站中有風險的登入報告"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: e21b7733bc0f6b8625fbdcec3529ad603ce306c1
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017

---
# Azure Active Directory 入口網站中有風險的登入報告
<a id="risky-sign-ins-report-in-the-azure-active-directory-portal" class="xliff"></a>

利用 Azure Active Directory (Azure AD) 中的安全性報告，您可以深入了解環境中使用者帳戶被盜用的可能性。 

Azure AD 會偵測使用者帳戶相關的可疑動作。 針對每個偵測到的動作，將會建立一筆稱為「風險事件」的記錄。 如需詳細資訊，請參閱 [Azure Active 風險事件](active-directory-identity-protection-risk-events.md)。 

偵測到的風險事件用來計算︰

- **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 如需詳細資訊，請參閱[有風險的登入](active-directory-identityprotection.md#risky-sign-ins)。 

- **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 如需詳細資訊，請參閱[標幟為有風險的使用者](active-directory-identityprotection.md#users-flagged-for-risk)。  

在 [Azure 入口網站](https://portal.azure.com)中，您可以在 [Azure Active Directory] 刀鋒視窗的 [安全性] 區段中找到安全性報告。 

![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## Azure Active Directory 免費和基本版本
<a id="azure-active-directory-free-and-basic-edition" class="xliff"></a>

Azure Active Directory 免費和基本版本會為您的使用者提供已偵測到的有風險登入清單。 風險事件報告會提供：

- **使用者** - 登入作業期間所使用的使用者名稱
- **IP** - 用來連接至 Azure Active Directory 的裝置 IP 位址
- **位置** - 用來連接至 Azure Active Directory 的位置
- **登入時間** - 執行登入的時間
- **狀態** - 登入狀態

此報告會提供選項讓您下載報告資料。

![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/01.png)

在調查有風險的登入後，您可以採取下列動作形式，提供意見反應給 Azure Active Directory：

- 解決
- 標記為誤判
- 略過
- 重新啟動

![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/21.png)

如需詳細資訊，請參閱[手動關閉風險事件](active-directory-identityprotection.md#closing-risk-events-manually)。

## Azure Active Directory Premium Edition
<a id="azure-active-directory-premium-editions" class="xliff"></a>

Azure Active Directory Premium Edition 中有風險的登入報告可為您提供：

- 關於已偵測到之[風險事件類型](active-directory-identity-protection-risk-events.md)的彙總資訊

- 下載報告的選項


![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/456.png)


當您選取風險事件時，即會取得這個風險事件的詳細報告檢視，讓您能夠：

- 選擇設定[使用者風險補救原則](active-directory-identityprotection.md#user-risk-security-policy)  

- 檢閱風險事件的偵測時間軸  

- 檢閱已偵測到此風險事件的使用者清單

- [手動關閉風險事件](active-directory-identityprotection.md#closing-risk-events-manually)或重新啟動已手動關閉的風險事件。 


![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/457.png)

當您選取使用者時，即會取得這位使用者的詳細報告檢視，讓您能夠：

- 開啟 [所有登入] 檢視

- 重設使用者的密碼

- 關閉所有事件

- 調查針對該使用者報告的風險事件。 


![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/324.png)


若要調查風險事件，請從清單中選取一項。  
這會開啟這個風險事件的 [詳細資料] 刀鋒視窗。 在 [詳細資料] 刀鋒視窗中，您可以選擇[手動關閉風險事件](active-directory-identityprotection.md#closing-risk-events-manually)或重新啟動已手動關閉的風險事件。 


![有風險的登入](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## 後續步驟
<a id="next-steps" class="xliff"></a>

- 如需 Azure Active Directory Identity Protection 的詳細資訊，請參閱 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)。


