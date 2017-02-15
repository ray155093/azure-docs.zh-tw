---
title: "Azure 資訊安全中心的權限 | Microsoft Docs"
description: "本文說明 Azure 資訊安全中心如何使用角色型存取控制，將權限指派給使用者，並識別每個角色允許的動作。"
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Azure 資訊安全中心的權限

Azure 資訊安全中心會使用[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)，以提供可在 Azure 中指派給使用者、群組與服務的[內建角色](../active-directory/role-based-access-built-in-roles.md)。

資訊安全中心會評估資源的組態，以識別安全性問題與弱點。 在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與資源相關的項目。

## <a name="roles-and-allowed-actions"></a>角色和允許的動作

下表會顯示資訊安全中心的角色和允許的動作。 X 表示該角色允許的動作。

| 角色 | 編輯安全性原則 | 針對資源套用安全性建議 | 修復或解除警示 | 檢視訂用帳戶的警示 | 檢視特定資源的警示 |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 訂用帳戶擁有者 | X | X | X | X | X |
| 訂用帳戶參與者 | X | X | X | X | X |
| 資源群組擁有者 | -- | X | -- | -- | X |
| 資源群組參與者 | -- | X | -- | -- | X |
| 讀取者 | -- | -- | -- | X | X |

> [!NOTE]
> 我們建議您指派所需的最寬鬆角色，以便使用者完成其工作。 例如，將「讀取者」角色指派給只需要檢視資源安全性狀態的相關資訊，但不採取行動的使用者，例如套用建議或編輯原則。
>
>

## <a name="next-steps"></a>後續步驟
本文說明資訊安全中心如何使用 RBAC，將權限指派給使用者，並識別每個角色允許的動作。 現在，您已熟悉監視您的訂用帳戶的安全性狀態所需的角色指派，編輯安全性原則和套用建議，接著了解如何︰

- [在資訊安全中心設定安全性原則](security-center-policies.md)
- [管理資訊安全中心的安全性建議](security-center-recommendations.md)
- [監視您的 Azure 資源的安全性健全狀況](security-center-monitoring.md)
- [在資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
- [監視合作夥伴安全性解決方案](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


