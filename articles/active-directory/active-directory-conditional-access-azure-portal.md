---
title: "Azure Active Directory 條件式存取 | Microsoft Docs"
description: "使用條件式存取控制，Azure Active Directory 會在驗證應用程式的存取權時，檢查特定的條件。"
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
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4dad4bd824f199562cb972c98cfcb452f2823828
ms.openlocfilehash: b85b10b9504c5efa7ec05b92b544ad777e3abacc
ms.lasthandoff: 02/16/2017


---
# <a name="conditional-access-in-azure-active-directory---preview"></a>Azure Active Directory 中的條件式存取 - 預覽

> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-conditional-access-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-conditional-access.md)


本主題中概述的行為目前為[預覽](active-directory-preview-explainer.md)狀態。

在行動第一、雲端第一的世界中，Azure Active Directory 可讓您從任何地方單一登入至裝置、應用程式和服務。 隨著裝置 (包括 BYOD)、在公司網路外部作業，以及協力廠商 SaaS 應用程式的激增，IT 專業人員面臨到兩個相對的目標︰

- 讓使用者隨時隨地都具有生產力
- 隨時保護公司資產

為了改善生產力，Azure Active Directory 為使用者提供廣泛的選項，以便存取您的公司資產。 透過應用程式存取管理，Azure Active Directory 可讓您確保只有「適當人員」可以存取您的應用程式。 如果您想要更充分掌控適當人員在某些情況下如何存取您的資源，該怎麼辦？ 如果您甚至有想要封鎖「適當人員」存取某些應用程式的條件，該怎麼辦？ 例如，您同意適當的人員從信任的網路存取某些應用程式；不過，您可能不希望他們從您不信任的網路存取這些應用程式。 您可以使用條件式存取解決這些問題。 

條件式存取是 Azure Active Directory 的功能，可讓您根據特定條件，強制控制您環境中的應用程式存取。 利用控制項，您可以將額外需求繫結到存取，也可以封鎖存取。 條件式存取的實作是以原則為基礎。 以原則為基礎的方法可簡化您的設定經驗，因為它會依循您對於存取需求的想法。  
您通常會使用以下列模式為基礎的陳述式，定義您的存取需求︰

![控制](./media/active-directory-conditional-access-azure-portal/10.png)

當您以真實世界的資訊取代出現兩次的 “this”，您會有可能看似熟悉的原則陳述式範例︰

*When contractors are trying to access our cloud apps from networks that are not trusted, then block access.*

上述的原則陳述式凸顯了條件式存取的功效。 您雖然可以讓約聘人員基本上存取您的雲端應用程式 (**誰**)，但利用條件式存取，您也可以定義在哪些條件下可能存取 (**如何**)。 

在 Azure Active Directory 條件式存取的內容中： 

- "**When this happens**" 稱為**條件陳述式**
- "**Then do this**" 成為**控制項**

![控制](./media/active-directory-conditional-access-azure-portal/11.png)

條件陳述式與控制項的組合代表條件式存取原則。

![控制](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>控制

在條件式存取原則中，控制項定義滿足條件陳述式時所應發生的狀況。  
利用控制項，您可以封鎖存取，或允許有額外需求的存取。
當您設定可允許存取的原則時，您必須選取至少一個需求。   

目前的 Azure Active Directory 實作可讓您設定下列需求︰ 

![控制](./media/active-directory-conditional-access-azure-portal/05.png)

- **Multi-Factor Authentication** - 您可以透過 Multi-Factor Authentication 來要求增強式驗證。 身為提供者，您可以使用 Azure Multi-Factor Authentication，或使用結合 Active Directory Federation Services (AD FS) 的內部部署多重要素驗證提供者。 對於未獲授權但已取得有效使用者之認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。

- **符合規範的裝置** - 您可以在裝置層級設定條件式存取原則。 您可以設定一個原則：只能夠讓符合規範的電腦，或已在行動裝置管理應用程式中註冊的行動裝置存取您組織的資源。 例如，您可以使用 Intune 來檢查裝置相容性，然後向 Azure AD 回報，以便在使用者嘗試存取應用程式強制執行。 如需如何使用 Intune 來保護應用程式和資料的詳細指引，請參閱「使用 Microsoft Intune 保護應用程式和資料」。 您也可以使用 Intune 來強制進行遺失或遭竊裝置的資料保護。 如需詳細資訊，請參閱「使用 Microsoft Intune 搭配完整或選擇性抹除協助保護您的資料」。

- **已加入網域的裝置** – 您可以要求您用來連接到 Azure Active Directory 的裝置是已加入網域的裝置。 此原則適用於 Windows 桌上型電腦、膝上型電腦和企業平板電腦。 如需有關如何設定讓已加入網域的裝置自動向 Azure AD 註冊的詳細資訊，請參閱[讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration.md)。

如果您在一個條件式存取原則中選取多個需求，您也可以設定您的需求以便套用它們。 您可以選擇需要所有選取的控制項，或只需要其中一個控制項。

![控制](./media/active-directory-conditional-access-azure-portal/06.png)


## <a name="condition-statement"></a>條件陳述式

上一節介紹了以控制項形式封鎖或限制資源存取的支援選項。 在條件式存取原則中，您可以定義必須符合才能以條件陳述式的形式套用您的控制項的準則。  

您可以將下列指派包含在您的條件陳述式中︰
    
![控制](./media/active-directory-conditional-access-azure-portal/07.png)


- **誰** - 在許多情況下，您想要讓控制項套用至一組特定的使用者。 在條件陳述式中，您可以選取原則套用至的使用者和群組，以定義這組使用者。 如有必要，您也可以明確豁免一組使用者，進行從您的原則中排除這些使用者。  
選取使用者和群組，您可以定義您的原則套用至的使用者範圍。    

    ![控制](./media/active-directory-conditional-access-azure-portal/08.png)



- **什麼** - 從保護的觀點而言，有些在您的環境中執行的應用程式通常需要更多關注 (相較於其他應用程式)。 例如，這會影響有權存取敏感性資料的應用程式。 選取雲端應用程式，您可以定義您的原則套用至的雲端應用程式範圍。 如有必要，您也可以從您的原則中明確排除一組應用程式。 

    ![控制](./media/active-directory-conditional-access-azure-portal/09.png)


- **如何** - 只要您的應用程式存取是在您可以控制的條件下執行，就可能不需要對使用者存取您雲端應用程式的方式強加其他控制項。 不過，如果從不受信任的網路或不符合規範的裝置執行您的雲端應用程式存取，情況可能會有所不同。 在條件陳述式中，您可以定義某些存取條件，其對應用程式存取的執行方式有額外需求。

    ![條件](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>條件

在目前的 Azure Active Directory 實作中，您可以定義下列領域的條件︰


- **裝置平台** – 裝置平台的特點是您裝置執行的作業系統 (Android、iOS、Windows Phone、Windows)。 您可以定義包含的裝置平台以及從原則中豁免的裝置平台。  
若要使用原則中的裝置平台，請先將 [設定] 切換為 [是]，然後選取原則套用至的所有或個別裝置平台。 如果您選取個別的裝置平台，原則就只會影響這些平台。 在此情況下，其他支援平台的登入不受此原則影響。 

    ![條件](./media/active-directory-conditional-access-azure-portal/02.png)

- **位置** - 您用來連接到 Azure Active Directory 之用戶端 IP 位址所識別的位置。 此條件要求您需熟悉信任的 IP。 信任的 IP 是 Multi-Factor Authentication 的功能，可讓您定義信任的 IP 位址範圍，以代表您組織的區域內部網路。 當您設定位置條件時，信任的 IP 可讓您區分從您組織的網路與所有其他位置進行的連線。 如需詳細資訊，請參閱[信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。  
您可以包含所有位置或所有信任的 IP，而且您可以排除所有信任的 IP。

    ![條件](./media/active-directory-conditional-access-azure-portal/03.png)


- **用戶端應用程式** - 用戶端應用程式可以是您用來連接到 Azure Active Directory 的一般層級應用程式 (Web 瀏覽器、行動應用程式、桌面用戶端)，或者您可以特別選取 Exchange Active Sync。  
舊式驗證是指使用基本驗證的用戶端，例如，未使用新式驗證的舊版 Office 用戶端。 舊式驗證目前不支援條件式存取。

    ![條件](./media/active-directory-conditional-access-azure-portal/04.png)


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
 
否，您目前無法在條件式存取原則中使用 Exchange ActiveSync。


### <a name="what-happens-if-i-require-multi-factor-authentication-or-a-compliant-device"></a>如果我要求多重要素驗證或符合規範裝置會發生什麼情況？

目前，會提示使用者使用與裝置無關的多重要素驗證。


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
例如，有權存取敏感性資料的應用程式。 如果您想要對這些應用程式新增另一層的保護，您可以設定當使用者存取這些應用程式時需要 Multi-Factor Authentication 的條件式存取原則。


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

