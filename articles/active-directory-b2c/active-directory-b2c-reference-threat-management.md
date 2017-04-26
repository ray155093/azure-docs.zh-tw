---
title: "Azure B2C 的威脅管理 | Microsoft Docs"
description: "在 Azure B2C 中偵測 DOS 攻擊和密碼攻擊並降低其風險的技術。"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C︰威脅管理
威脅管理包括保護系統和網路，不讓其遭受攻擊。 拒絕服務 (DOS) 會影響可用性，並讓預期使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。 Microsoft Azure Active Directory B2C 具備內建功能，可透過多種方式保護您的資料，不讓資料遭受這些威脅。 

## <a name="denial-of-service-attack"></a>拒絕服務的攻擊

Azure AD B2C 使用偵測和降低風險的技術 (例如 SYN Cookie、速率和連線限制)，來保護基礎資源，不讓資源遭受到這些攻擊。  

## <a name="password-attacks"></a>密碼攻擊

Azure AD B2C 也備有降低風險的技術，可防範密碼攻擊。  這項技術包含暴力密碼破解攻擊和字典密碼破解攻擊。  使用者所設定的密碼必須具備適當的複雜性。  Azure AD B2C 會分析要求的完整性，透過智慧方式以各種訊號來區分預期使用者以及駭客與殭屍網路。 B2C 可提供複雜的策略，以根據所輸入的密碼和攻擊可能性來鎖定帳戶。

[Microsoft 威脅管理的詳細資訊](https://www.microsoft.com/trustcenter/security/threatmanagement)
