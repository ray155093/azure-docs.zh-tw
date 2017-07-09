---
title: "Azure Active Directory 中條件式存取的最佳做法 | Microsoft Docs"
description: "了解您在設定條件式存取原則時應該知道的事件及應避免的動作。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: 9cecbfd1fd5db8fffc9fbdfb2d6ca949b6ff385a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/22/2017


---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中條件式存取的最佳做法

此主題提供與您在設定條件式存取原則時應該知道的事件及應避免的動作相關的資訊。 在閱讀本主題前，您應熟悉 [Azure Active Directory 中條件式存取 (英文)](active-directory-conditional-access-azure-portal.md) 中所列的概念與術語

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>我需要將使用者指派給我的原則嗎？

在設定條件式存取原則時，您應該至少指派一個群組給它。 永遠不會觸發未指派使用者和群組的條件式存取原則。

當您打算將數個使用者和群組指派給某個原則時，您一開始應該僅指派一個使用者或群組，然後測試您的組態。 如果您的原則如預期般運作，您可以接著新增其他指派。  


### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，若要觸發原則，則必須滿足所有的指派。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線，您可以藉由下列方式達成︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  
針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件式存取是否適用於 Exchange ActiveSync？

是，您可以在條件式存取原則中使用 Exchange ActiveSync。


## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構可為您提供絕佳的組態彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個組態原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：


**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。

- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。

- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。


**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。


## <a name="common-scenarios"></a>常見案例

### <a name="requiring-multi-factor-authentication-for-apps"></a>應用程式需要 Multi-Factor Authentication

許多環境中的應用程式需要比其他應用程式更高層級的保護。
例如，有權存取敏感性資料的應用程式。
如果您想要對這些應用程式新增另一層的保護，您可以設定當使用者存取這些應用程式時需要 Multi-Factor Authentication 的條件式存取原則。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>從不受信任的網路存取時需要 Multi-Factor Authentication

此案例類似於前一個案例，因為這會新增 Multi-Factor Authentication 的需求。
不過，主要差異在於這項需求的條件。  
雖然前一個案例的焦點在於有權存取敏感性資料的應用程式，而此案例的焦點則在於信任的位置。  
換句話說，如果使用者從您不信任的網路存取應用程式，您可能需要 Multi-Factor Authentication。


### <a name="only-trusted-devices-can-access-office-365-services"></a>只有受信任的裝置可以存取 Office 365 服務

如果您在自己的環境中使用 Intune，您可以在 Azure 主控台中立即開始使用條件式存取原則介面。

許多 Intune 客戶都使用條件式存取來確保只有受信任的裝置可以存取 Office 365 服務。 這表示行動裝置已向 Intune 進行註冊並符合合規性原則需求，而且 Windows 電腦已加入內部部署網域。 您不需要為每項 Office 365 服務設定相同的原則，是一項重大改進。  當您建立新原則時，請設定雲端應用程式包含您想要使用條件式存取保護的每個 O365 應用程式。

## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

