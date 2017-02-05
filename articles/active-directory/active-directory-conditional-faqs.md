---
title: "Azure Active Directory 條件式存取常見問題集 | Microsoft Docs"
description: "關於條件式存取的常見問題集  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3980b99206c9f0c81ba3d03778777878e25db7c8


---
# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory 條件式存取常見問題集
## <a name="which-applications-work-with-conditional-access-policies"></a>哪些應用程式會使用條件式存取原則？
**答：** 請參閱主題 [條件式存取 - 支援哪些應用程式](active-directory-conditional-access-supported-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>是否會針對 B2B 共同作業與來賓使用者強制套用條件式存取原則？
**答：** 系統會針對 B2B 共同作業使用者強制套用原則。 不過，在某些情況下，使用者可能無法滿足原則需求，例如，如果組織不支援 Multi-Factor Authentication。 

目前不會針對 SharePoint 來賓使用者強制套用原則。 來賓關聯性是在 SharePoint 內進行維護。 驗證伺服器上的來賓使用者帳戶不會受限於存取原則。 您可以在 SharePoint 上管理來賓存取。

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 原則也適用於商務用 OneDrive 嗎？
**答：** 是。

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>為什麼我無法在用戶端應用程式 (例如 Word 或 Outlook) 上設定原則？
**答：** 條件式存取原則會設定存取服務的需求，並在該服務發生驗證時強制套用。 原則不是直接設定於用戶端應用程式上，而是在它呼叫服務時加以套用。 例如，在 SharePoint 上設定的原則會套用至呼叫 SharePoint 的用戶端，而 Exchange 上設定的原則會套用至 Outlook。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>是否會對服務帳戶套用條件式存取原則？
**答：** 所有使用者帳戶都會套用條件式存取原則。 這包括做為服務帳戶的使用者帳戶。 在許多情況下，自動執行的服務帳戶無法符合原則。 例如，在需要 MFA 時即是如此。 在這些情況下，可以使用條件式存取原則管理設定，將服務帳戶排除在原則套用範圍之外。 在此深入了解如何對使用者套用原則。




<!--HONumber=Dec16_HO5-->


