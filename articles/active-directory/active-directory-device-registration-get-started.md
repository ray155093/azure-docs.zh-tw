---
title: "如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊 | Microsoft Docs"
description: "設定讓已加入網域的 Windows 裝置以自動和無訊息方式向 Azure Active Directory 註冊。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.contentlocale: zh-tw
ms.lasthandoff: 03/10/2017

---
# <a name="get-started-with-azure-active-directory-device-registration"></a>開始使用 Azure Active Directory 裝置註冊

Azure Active Directory 裝置註冊是裝置型條件式存取案例的基礎。 當裝置已註冊時，Azure Active Directory 裝置註冊會在使用者登入時為裝置提供用來驗證裝置的身分識別。 然後已驗證的裝置和裝置的屬性即可用來對裝載於雲端和內部部署的應用程式，強制執行條件式存取原則。

與 Microsoft Intune 這類的行動裝置管理 (MDM) 解決方案結合時，將會以裝置的其他相關資訊更新 Azure Active Directory 中的裝置屬性。 這可讓您建立條件式存取規則，強制讓裝置的存取符合您的安全性和相容性標準。 如需如何在 Microsoft Intune 中註冊裝置的詳細資訊，請參閱＜在 Intune 中註冊要管理的裝置＞。
由 Azure Active Directory 裝置註冊 Azure Active Directory 裝置註冊啟用的案例包括對 iOS、Android 與 Windows 裝置的支援。 利用 Azure AD 裝置註冊的個別案例可能會有更明確的需求和平台支援。 

這些案例如下所示︰

- **適用於包含 Microsoft Intune 之 Office 365 應用程式的條件式存取：**IT 系統管理員可以佈建條件式存取裝置原則來保護公司資源，同時允許符合規範之裝置上的資訊工作者存取服務。 如需詳細資訊，請參閱 Office 365 服務的條件式存取裝置原則。

- **內部部署環境裝載之應用程式的條件式存取：**您可以使用已註冊的裝置搭配適用於已設定為使用 Windows Server 2012 R2 AD FS 之應用程式的存取原則。 如需有關設定內部部署之條件式存取的詳細資訊，請參閱[使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](active-directory-device-registration-on-premises-setup.md)。

## <a name="setting-up-azure-active-directory-device-registration"></a>設定 Azure Active Directory 裝置註冊

若要設定裝置註冊，您有多個選項：

- 裝置可以在加入 Azure AD 網域時註冊。 如需有關此主題的詳細資訊，您可以深入了解 Azure AD 加入與讓使用者加入 Azure AD 網域所需的設定。

- 裝置可以在使用者在個人裝置上新增公司或學校帳戶到 Windows 或在行動裝置連線到需要註冊的公司資源時註冊。 若要確保此行為，您必須在 Azure 入口網站中啟用 Azure AD 裝置註冊。 

- 裝置可以針對已加入網域的傳統電腦使用自動裝置註冊。 為確保此行為，您必須先設定 Azure AD Connect，才能進行自動裝置註冊。

如需最新的指示，請參閱[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)。  
您也可以使用 Azure Active Directory 中的「系統管理員入口網站」來檢視及啟用/停用已註冊的裝置。

## <a name="enable-the-azure-active-directory-device-registration-service"></a>啟用 Azure Active Directory 裝置註冊服務

**啟用 Azure Active Directory 裝置註冊服務：**

1.  以系統管理員身分登入 Microsoft Azure 入口網站。

2.  在左窗格中選取 [Active Directory]。

3.  在 [目錄] 索引標籤中，選取您的目錄。

4.  按一下 [設定]。

5.  捲動到 [裝置]。

6.  針對 [使用者可以向 Azure AD 註冊其裝置] 選取 [全部]。

7.  選取您要依每位使用者授權的裝置數目上限。

Microsoft Intune 註冊或 Office 365 的行動裝置管理要求必須執行裝置註冊。 如果您已設定任一服務，則會選取 [全部] 並停用 [無]。 請確定已正確設定它們，而且它們有適當的授權。

根據預設，服務未啟用雙因素驗證。 不過，建議在註冊裝置時使用雙因素驗證。

- 在要求此服務的雙因素驗證之前，您必須先在 Azure Active Directory 中設定雙因素驗證提供者以及針對 Multi-Factor Authentication 設定您的使用者帳戶，請參閱將 Multi-Factor Authentication 新增至 Azure Active Directory

- 如果您搭配使用 AD FS 與 Windows Server 2012 R2，則必須在 AD FS 中設定雙因素驗證模組，請參閱＜搭配使用 Multi-Factor Authentication 與 Active Directory 同盟服務＞。

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>在 Azure Active Directory 中檢視和管理裝置物件

從 Azure 系統管理員入口網站，您可以檢視、封鎖和解除封鎖裝置。 已封鎖的裝置將無法再存取已設為僅允許已註冊裝置的應用程式。

**在 Azure Active Directory 中檢視及管理裝置物件：**
 
1.  以系統管理員身分登入 Microsoft Azure 入口網站。

2.  在左窗格中選取 [Active Directory] 。

3.  選取您的目錄。

4.  選取 [使用者]。 

5.  按一下您要查看其裝置的使用者。

6.  選取 [裝置]。

7.  選取 [註冊的裝置]。

現在您可以檢閱、封鎖或解除封鎖使用者已註冊的裝置。
已加入內部部署網域且已自動註冊的 Windows 10 裝置不會出現在 [使用者] 索引標籤下。 請使用 Get-MsolDevice PowerShell 命令來尋找您的所有企業裝置。 


## <a name="next-steps"></a>後續步驟

若要設定自動裝置註冊，請參閱[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)。



