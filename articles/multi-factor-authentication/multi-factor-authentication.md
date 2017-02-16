---
title: "了解 Azure MFA 中的雙步驟驗證 | Microsoft Docs"
description: "何謂 Azure Multi-factor Authentication、為什麼使用 MFA，還有 Multi-Factor Authentication 用戶端、不同的方法及可用版本的詳細資訊。 "
keywords: "MFA 的簡介, mfa 概觀, 什麼是 mfa"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 757a26ed6c39e6eb498a4f717a2eb5da3cb5c0a6


---
# <a name="what-is-azure-multi-factor-authentication"></a>什麼是 Azure Multi-Factor Authentication？
雙步驟驗證是需要多種驗證方法，並在使用者登入和交易中新增重要的第二層安全性的驗證方法。 其運作方式需要下列其中任何二或多個驗證方法：

* 您知道的某些資訊 (通常是密碼)
* 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
* 您身上的某些特徵 (生物識別技術)

<center>![使用者名稱和密碼](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![憑證](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智慧型手機](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智慧卡](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![虛擬智慧卡](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![使用者名稱和密碼](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些驗證方法 (包括電話、文字訊息，或行動應用程式驗證) 來提供強大的驗證功能。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/WA-MFA-Overview/player]
>
>

## <a name="why-use-azure-multi-factor-authentication"></a>為何使用 Azure Multi-Factor Authentication？
與以前比較起來，現今人們連線網路的時間越來越長。 透過智慧型手機、平板電腦、膝上型電腦以及電腦，人們有幾種不同選擇可隨時用來連線網路並維持連線。 人們可以從任何地方存取他們的帳戶與應用程式，這表示他們可以完成更多工作並為客戶提供更好的服務。

Azure Multi-Factor Authentication 是一個容易使用、可調整且可靠的解決方案，可提供第二種驗證方法讓您的使用者永遠受到保護。

| ![容易使用](./media/multi-factor-authentication/simple.png) | ![可調整](./media/multi-factor-authentication/scalable.png) | ![永遠受到保護](./media/multi-factor-authentication/protected.png) | ![可靠](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **容易使用** |**可調整** |**永遠受到保護** |**可靠** |

* **容易使用** - Azure Multi-Factor Authentication 很容易設定及使用。 隨附於 Azure Multi-Factor Authentication 的額外保護可讓使用者管理他們自己的裝置。 最棒的是，在許多情況下，只需簡單按幾下滑鼠即可進行設定。
* **可調整** - Azure Multi-Factor Authentication 採用雲端技術且與您內部部署的 AD 與自訂應用程式整合。 此保護功能甚至可以擴充以因應您高任務關鍵性的狀況。
* **永遠受到保護** - Azure Multi-Factor Authentication 使用最高工業標準提供強大驗證功能。
* **可靠** - 我們保證 Azure Multi-Factor Authentication 的可用性可達到 99.9%。 當服務無法接收或處理雙步驟驗證的驗證要求時，服務會被視為無法使用。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]
>
>

## <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication 的作用
雙步驟驗證的安全性仰賴其分層方法。 使用多重驗證方法會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探您的密碼，在不持有信任裝置的情況下便沒有任何意義。 如果您遺失裝置，拾獲該裝置的人仍然無法使用此裝置，除非他或她剛好知道您的密碼。

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]
>
>

## <a name="methods-available-for-multi-factor-authentication"></a>適用於 Multi-Factor Authentication 的方法
當使用者登入時，系統會將額外的驗證要求傳送給使用者。 以下是適用於這個第二次驗證的方法清單。

| 驗證方法 | 說明 |
| --- | --- |
| 撥打電話 |撥打使用者的手機，要求他們驗證簽署。 按下 # 鍵可完成驗證程序。 這是可設定的選項，您可以變更為指定的代碼。 |
| 簡訊 |會將含 6 位數代碼的簡訊傳送到使用者的智慧型手機。 輸入此代碼可完成驗證程序。 |
| 行動應用程式通知 |將驗證要求傳送到使用者的智慧型手機，要求他們在行動應用程式中選取 [驗證] 來完成驗證。 如果應用程式通知是主要驗證方法，這種情況便會發生。 如果它們在未登入時收到這個通知，他們可以提報詐騙。 |
| 透過行動應用程式的驗證碼 |使用者裝置上的行動應用程式會產生驗證碼。 如果您將驗證碼選為主要驗證方法，這種情況便會發生。 |

針對行動應用程式驗證方法，Azure Multi-Factor Authentication 可與智慧型手機的協力廠商驗證應用程式搭配使用。 但是，我們建議使用適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 的 Microsoft 驗證器應用程式。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure Multi-Factor Authentication 版本
Azure Multi-Factor Authentication 備有三個不同的版本。

| 版本 | 說明 |
| --- | --- |
| Multi-Factor Authentication for Office 365 |這個版本專門搭配 Office 365 應用程式運作，並且可從 Office 365 入口網站管理。 系統管理員現在可以使用雙步驟驗證來協助保護其 Office 365 資源的安全。 此版本隨附於 Office 365 訂用帳戶。 |
| 適用於 Azure 系統管理員的 Multi-Factor Authentication |所有 Azure 管理員皆可免費使用與 Office 365 雙步驟驗證相同的功能子集。 每個 Azure 訂用帳戶的管理帳戶皆可啟用這項功能以獲得額外的保護。 想要存取 Azure 入口網站以建立 VM 或網站、管理儲存體或使用任何其他 Azure 服務的系統管理員，可在其系統管理員帳戶中新增 MFA。 |
| Azure Multi-Factor Authentication | 又稱為「完整」版本，Azure Multi-Factor Authentication 提供最豐富的功能。 它能透過 [Azure 傳統入口網站](https://manage.windowsazure.com)、進階報告及支援一系列內部部署和雲端應用程式來提供其他組態選項。 Azure Multi-Factor Authentication 隨附於 Azure Active Directory Premium 和 Enterprise Mobility Suite 中，並可以在雲端中部署或進行內部部署。 請參閱下方[取得 Azure Multi-Factor Authentication 的其他方法](multi-factor-authentication.md#how-to-get-azure-multi-factor-authentication)。 |

## <a name="feature-comparison-of-versions"></a>版本的功能比較
下表提供 Azure Multi-Factor Authentication 各版本中可用的功能清單。

> [!NOTE]
> 此比較表會討論每個 Multi-Factor Authentication 版本中所包含的功能。 如果您擁有完整的 Azure Multi-Factor Authentication 服務，則依您使用的是[雲端中的 MFA 或內部部署 MFA](multi-factor-authentication-get-started.md) 而定，某些功能可能無法使用。


| 功能 | Multi-Factor Authentication for Office 365 | 適用於 Azure 系統管理員的 Multi-Factor Authentication | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| 透過 MFA 保護系統管理員帳戶 |● |● (僅適用於 Azure 系統管理員帳戶) |● |
| 以行動應用程式做為第二個因素 |● |● |● |
| 以撥打電話做為第二個因素 |● |● |● |
| 以 SMS 做為第二個因素 |● |● |● |
| 用戶端應用程式密碼不支援 MFA |● |● |● |
| 系統管理員控制驗證方法 |● |● |● |
| PIN 模式 | | |● |
| 詐騙警示 | | |● |
| MFA 報告 | | |● |
| 一次性略過 | | |● |
| 通話的自訂問候語 | | |● |
| 自訂的通話來電者 ID | | |● |
| 事件確認 | | |● |
| 信任的 IP | | |● |
| 記住受信任裝置的 MFA |● |● |● |
| MFA SDK | | |● 需要 Multi-Factor Auth Provider 和完整的 Azure 訂用帳戶 |
| 內部部署應用程式的 MFA | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>如何取得 Azure Multi-Factor Authentication
如果您想要 Azure Multi-Factor Authentication 所提供的完整功能，有數個選項︰

1. 購買 Azure Multi-Factor Authentication 授權，並將其指派給您的使用者。
2. 購買其中隨附 Azure Multi-Factor Authentication 的授權 (例如 Azure Active Directory Premium、Enterprise Mobility Suite 或 Enterprise Cloud Suite)，並將其指派給您的使用者。
3. 在 Azure 訂用帳戶中建立 Azure Multi-Factor Authentication 提供者。 使用 Azure Multi-Factor Authentication 提供者時，有兩種使用量模型可透過您的 Azure 訂用帳戶計費：  
   * **每位使用者**。 適用於想要為一群定期需要驗證之固定數量員工啟用雙步驟驗證的企業。  
   * **每次驗證**。 適用於想要為大量不定期需要驗證之外部使用者啟用雙步驟驗證的企業。  

Azure Multi-Factor Authentication 為雲端與伺服器提供可選取的驗證方法。 這表示您可以選擇可供使用者使用的方法：撥打電話、簡訊、應用程式通知或應用程式程式碼。 如需詳細資訊，請參閱 [可選取的驗證方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)。

如需定價詳細資料，請參閱 [Azure MFA 定價](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="next-steps"></a>後續步驟
若要開始使用 Azure Multi-Factor Authentication，您的第一個步驟是 [從雲端或內部部署的 MFA 選擇其一](multi-factor-authentication-get-started.md)



<!--HONumber=Jan17_HO5-->


