---
title: "將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10 | Microsoft Docs"
description: "說明系統管理員應如何設定群組原則，使裝置成為企業網路中已加入網域的裝置。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10
「加入網域」是過去 15 年來組織使用連接的裝置進行工作的傳統方式。 這讓使用者能夠使用他們的 Windows Server Active Directory (Active Directory) 工作或學校帳戶來登入其裝置，並允許 IT 能夠完全管理這些裝置。 組織通常依賴映像處理方法將裝置佈建給使用者，且通常使用 System Center Configuration Manager (SCCM) 或群組原則加以管理。


當您將裝置連接到 Azure Active Directory (Azure AD) 後，Windows 10 中的「網域加入」會提供您下列優點：

* 從任何位置單一登入 (SSO) 至 Azure AD 資源
* 使用工作或學校帳戶存取企業 Windows 市集 (不需要 Microsoft 帳戶)
* 使用工作或學校帳戶進行符合企業標準的跨裝置使用者設定漫遊 (不需要 Microsoft 帳戶)
* 透過 Windows Hello 企業版和 Windows Hello 進行工作或學校帳戶的增強式驗證與便利的登入
* 能夠限制為只能存取遵循組織裝置群組原則設定的裝置

## <a name="prerequisites"></a>必要條件
「網域加入」仍持續有用。 但是，若要透過 Azure AD 享有 SSO、使用工作或學校帳戶漫遊設定，以及使用工作或學校帳戶存取 Windows 市集的好處，您必須符合下列條件：

* Azure AD 訂用帳戶
* Azure AD Connect，以將內部部署目錄擴充至 Azure AD
* 設定來將已加入網域的裝置連接到 Azure AD 的原則
* 裝置的 Windows 10 組建 (組建 10551 或更新版本)

若要啟用 Windows Hello 企業版和 Windows Hello，您還需要下列項目：

- 適用於使用者憑證發行的**公開金鑰基礎結構 (PKI)**。

- **System Center Configuration Manager 最新分支** - 您必須安裝 1606 版或更新版本。  
如需詳細資訊，請參閱： 
    - [System Center Configuration Manager 文件](https://technet.microsoft.com/library/mt346023.aspx)
    - [System Center Configuration Manager 團隊部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [ System Center Configuration Manager 中的 Windows Hello 企業版](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

若要以 PKI 以外的方式進行部署，您可以執行下列作業：

* 建立幾個 Windows Server 2016 Active Directory 網域服務的網域控制站。

若要啟用條件式存取，您可以建立群組原則設定，允許存取已加入網域的裝置，而不需其他部署。 若要根據裝置的合規性來管理存取控制，您需要下列項目：

* 適用於 Windows Hello 企業版案例的 System Center Configuration Manager 目前分支 (1606 或更新版本)

## <a name="deployment-instructions"></a>部署指示

若要進行部署，請依照[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)中所列的步驟操作

## <a name="next-step"></a>後續步驟
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)


