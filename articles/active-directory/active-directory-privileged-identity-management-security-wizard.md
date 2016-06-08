<properties
   pageTitle="Azure AD Privileged Identity Management 安全性精靈"
   description="第一次使用 Azure Active Directory Privileged Identity Management 擴充功能時，您將會看到安全性精靈。本文說明使用精靈的步驟。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management 安全性精靈

如果您是貴組織中執行 Azure Privileged Identity Management (PIM) 的第一個人，您就會看到精靈。精靈會協助您了解特殊權限身分識別的安全性風險，以及如何使用 PIM 來降低這些風險。如果您想要稍後再進行變更，就不需要在精靈中對現有的角色指派進行任何變更。

## 未來展望

在貴組織開始使用 PIM 之前，所有的角色指派都是永久的︰即使使用者目前不需要其權限，他們都一直保有這些角色。精靈的第一個步驟會顯示高特殊權限角色的清單，以及這些角色中目前有多少使用者。如果不清楚一或多個角色，您可以向內切入某個特定角色來深入了解使用者。

精靈的第二個步驟會讓您有機會變更系統管理員的角色指派。

> [AZURE.WARNING] 您必須至少有一個全域系統管理員，這一點很重要，而且要有多個具備組織帳戶 (而非 Microsoft 帳戶) 的特殊權限角色管理員。如果只有一個特殊權限角色管理員，若該帳戶遭到刪除，組織就無法管理 PIM。此外，如果使用者具有 Microsoft 帳戶，請勿將角色指派變更為暫時性。如果您計劃要求 MFA 來啟用該角色，就會封鎖該使用者角色。


進行變更之後，將不會再次顯示精靈。下次當您或其他特殊權限角色管理員使用 PIM 時，您將會看到 PIM 儀表板。

- 如果您想要從角色新增或移除使用者，或將指派從永久變更為暫時，請參閱 [Azure AD Privileged Identity Management：如何開始安全性檢閱](active-directory-privileged-identity-management-how-to-add-role-to-user.md)。
- 如果您想要讓更多使用者存取管理 PIM，請參閱 [How to give access to manage Azure AD Privileged Identity Management (如何授與存取權以管理 Azure AD Privileged Identity Management)](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。



## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->