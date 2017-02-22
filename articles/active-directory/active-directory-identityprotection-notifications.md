---
title: "Azure Active Directory Identity Protection 通知| Microsoft Docs"
description: "了解通知如何支援您的調查活動。"
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce3379d5b5e883c6601c40aca191e8b84e3ad8d3
ms.openlocfilehash: 0170b5d2435f6e856478ee9e55ae26c626288f75


---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知
Azure AD Identity Protection 會傳送兩種自動化通知電子郵件，協助您管理使用者風險和風險事件：

* 使用者遭到入侵的警示電子郵件
* 每週精選文章電子郵件

## <a name="user-compromised-alert-email"></a>使用者遭到入侵的警示電子郵件
當 Azure AD Identity Protection 發現帳戶遭到入侵時，就會產生使用者遭到入侵的電子郵件警示。 此電子郵件包含 Identity Protection 儀表板中「標示有風險的使用者」報告的連結。 建議您立即調查遭入侵之帳戶的通知。

## <a name="weekly-digest-email"></a>每週精選文章電子郵件
每週精選文章電子郵件包含新風險事件的摘要。<br>
其中包括：

* 有風險的使用者
* 可疑的活動
* 偵測到的弱點
* Identity Protection 中相關報告的連結

<br>
![補救](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

您可以關閉每週精選文章電子郵件的傳送。
<br><br>
![使用者風險](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**開啟相關的組態對話方塊**：

1. 在 [Azure AD Identity Protection] 刀鋒視窗上，按一下 [設定]。
   <br><br>
   ![使用者風險原則](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. 在 [一般] 區段中，按一下 [通知]。
   <br><br>
   ![使用者風險原則](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>另請參閱
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)



<!--HONumber=Dec16_HO4-->


