---

title: "什麼是 Azure Active Directory 中的群組型授權？ | Microsoft Docs"
description: "描述 Azure Active Directory 群組型授權、如何運作、如何開始著手，以及最佳做法"
services: active-directory
keywords: "Azure AD 授權"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 1f4e0904c9025ce0b21d904acc7b959e823039ef
ms.lasthandoff: 02/22/2017


---

# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>什麼是 Azure Active Directory 中的群組型授權？

Microsoft 雲端服務 (例如 Office 365、Enterprise Mobility + Security、Dynamics CRM 及其他類似的產品) 會要求使用者必須獲指派權限，才能存取這些服務。 授權管理是透過其中一個管理入口網站 (Office、Azure) 及 PowerShell Cmdlet 對系統管理員公開。 授權指派狀態儲存在 Azure Active Directory 中，這是支援所有 Microsoft 雲端服務身分識別管理的基底基礎結構。

到目前為止，只能在個別使用者層級指派授權，這使得我們的客戶難以進行大規模的管理。 例如，若要根據組織變更 (例如使用者加入或離開組織或部門) 來新增或移除使用者授權，系統管理員通常必須撰寫複雜的 PowerShell 指令碼來對雲端服務進行個別的呼叫。

為了解決這些挑戰，我們導入了一項新的 Azure AD 授權管理系統功能：群組型授權。 現在已經可以將一或多個產品授權指派給群組。 Azure AD 會確保將授權指派給該群組的所有成員。 任何加入群組的新成員都將獲指派適當的授權，並在他們離開群組時將會移除這些授權。 如此一來，不需透過 PowerShell 來自動管理授權，即可依據每一使用者反映組織和部門結構中的變更。

## <a name="features"></a>特性

以下是群組型授權功能的主要特色：

- 可將授權指派給 Azure AD 中的任何安全性群組。 可使用 Azure AD Connect 從內部部署環境同步安全性群組、可直接在 Azure AD 中建立安全性群組 (也稱為僅限雲端的群組)，或透過 Azure AD 的「動態群組」功能自動建立安全性群組。

- 將產品授權指派給群組時，系統管理員可以停用該產品中的一或多個服務方案。 通常是組織尚未準備好開始產品中所包含的某項服務時，才這麼做，例如系統管理員想要將 Office 365 E3 產品指派給某個部門，但暫時停用 Yammer Enterprise 服務。

- 支援所有需要使用者層級授權的 Microsoft 雲端服務。 這包括所有 Office 365 產品、Enterprise Mobility + Security、Dynamics CRM 等。

- 群組型授權目前僅透過 [Azure 入口網站](https://portal.azure.com)提供。 客戶如果主要是使用其他管理入口網站 (例如 Office 365 入口網站) 來進行使用者和群組管理，可以繼續這麼做，但是將需要使用 Azure 入口網站來管理群組層級的授權。

- Azure AD 會自動管理因群組成員資格變更而產生的授權修改。 一般而言，當使用者加入或離開群組時，在成員資格變更的幾分鐘內，系統就會修改他們的授權。

- 一個使用者可以是已指定授權原則之多個群組的成員；它們也可以有一些直接指派給任何群組外使用者的授權。 產生的使用者狀態是所有已指派之產品與服務授權的組合。

- 在某些情況下，會無法將授權指派給使用者；例如，因為租用戶中的授權不足，或同時指派了衝突的服務。 系統管理員可以存取 Azure AD 無法為其完整處理群組授權的使用者相關資訊；他們可以接著根據該資訊採取更正動作。

- 在公開預覽期間，租用戶中必須要有 Azure AD Basic 或更高版本的付費或試用訂用帳戶，才能使用群組型授權管理。 此外，從群組繼承任何授權的每位使用者都必須獲指派付費 Azure AD 版本授權。

## <a name="next-steps"></a>後續步驟

若要深入了解其他透過群組型授權來管理授權的案例，請參閱

* [將授權指派給 Azure Active Directory 中的群組 (英文)](active-directory-licensing-group-assignment-azure-portal.md)
* [識別及解決 Azure Active Directory 中群組的授權問題 (英文)](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何將個別的已授權使用者移轉成 Azure Active Directory 中的群組型授權 (英文)](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](active-directory-licensing-group-advanced.md)

