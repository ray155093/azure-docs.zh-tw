---
title: "Azure Active Directory B2C：開始使用自訂原則 | Microsoft Docs"
description: "如何開始使用 Azure Active Directory B2C 自訂原則的主題"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C：開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文中的步驟之後，您的自訂原則將支援使用電子郵件和密碼執行「本機帳戶」註冊或登入。 您也會準備環境以新增其他識別提供者 (例如 Facebook 或 Azure AD)。  必須先完成這些步驟，才能繼續介紹 Azure AD B2C 識別經驗引擎的其他所有用法和許多基本概念。

## <a name="prerequisites"></a>必要條件

在繼續之前，請確定您有 Azure AD B2C 租用戶。 Azure AD B2C 租用戶是存放您的所有使用者、應用程式、原則等的容器。 如果您還沒有租用戶，則必須[建立租用戶](active-directory-b2c-get-started.md)。

### <a name="confirming-your-b2c-tenant"></a>確認您的 B2C 租用戶

由於自訂原則仍在私人預覽階段，請確認您的 Azure AD B2C 租用戶支援上傳自訂原則︰

1. 在 [Azure 入口網站](https://portal.azure.com)中，[切換至您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 Azure AD B2C 刀鋒視窗。
1. 按一下 [所有原則]。
1. 請確定 [上傳原則] 可用。  如果此按鈕已停用，請寄電子郵件到 AADB2CPreview@microsoft.com。

## <a name="set-up-keys-for-your-custom-policy"></a>設定自訂原則的金鑰

使用自訂原則的第一個必要步驟是設定金鑰。

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>下載入門套件和修改原則

自訂原則是一組需要上傳至 Azure AD B2C 租用戶的 XML 檔案。 我們提供入門套件給您開始使用。 入門套件包含︰

* 原則的[基底檔案](active-directory-b2c-overview-custom.md#policy-files)。 需要對基底做一些修改。
* 原則的[擴充檔案](active-directory-b2c-overview-custom.md#policy-files)。  大部分的設定變更都在這個檔案中完成。

現在就開始吧！

1. 從 GitHub 下載「Azure AD B2C 自訂原則入門套件」。  [下載 zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 或執行

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 開啟 `SocialIDPAndLocalAccounts` 資料夾。  此資料夾中的基底檔案 (`TrustFrameworkBase.xml`) 包含本機和社交/公司帳戶所需的內容。 社交內容不會干擾本機帳戶的啟動和執行步驟。
1. 開啟 `TrustFrameworkBase.xml`。  如果您需要 XML 編輯器，請嘗試[下載 Visual Studio 程式碼](https://code.visualstudio.com/download)，這是一個輕巧的跨平台編輯器。
1. 在 `TrustFrameworkPolicy` 根元素中，更新 `TenantId` 和 `PublicPolicyUri` 屬性，以您的 Azure AD B2C 租用戶取代 `{tenantName}`︰

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. 儲存檔案。
1. 開啟 `TrustFrameworkExtensions.xml`，同樣以您的 Azure AD B2C 租用戶取代 `{tenantName}`。 儲存檔案。
1. 開啟 `SignUpOrSignIn.xml`，同樣以您的 Azure AD B2C 租用戶取代 `{tenantName}`。 儲存檔案。

>[!NOTE]
>如果您的 XML 編輯器支援驗證，您可以根據入門套件根資料夾中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 結構描述檔案來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

## <a name="register-policy-engine-applications"></a>註冊原則引擎應用程式

Azure AD B2C 會要求您註冊兩個額外的應用程式，由引擎用來讓使用者註冊和登入。

>[!NOTE]
>下面，我們建立兩個支援使用本機帳戶登入的應用程式︰PolicyEngine (Web 應用程式) 和從 PolicyEngine 委派權限的 PolicyEngineProxy (原生應用程式)。 只有當 Azure AD B2C 租用戶需要使用本機帳戶時，才需要閱讀本節。

### <a name="create-the-policy-engine-application"></a>建立原則引擎應用程式

1. 在 [Azure 入口網站](https://portal.azure.com)中，[切換至您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)。
1. 開啟 `Azure Active Directory` 刀鋒視窗 (不是 Azure AD B2C 刀鋒視窗)。 您可能需要按一下 [> 更多服務] 才能找到它。
1. 選取 [應用程式註冊]。
1. 按一下 [+新增應用程式註冊]。
   * 名稱：`PolicyEngine`
   * 應用程式類型：`Web app/API`
   * 登入 URL：`https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` 其中`{tenantName}` 是您的 Azure AD B2C 租用戶。
1. 按一下 [建立] 。
1. 建立之後，選取新建立的應用程式 `PolicyEngine`，然後複製應用程式識別碼，儲存起來供稍後使用。

### <a name="create-the-policy-engine-proxy-application"></a>建立原則引擎 Proxy 應用程式

1. 選取 [應用程式註冊]。
1. 按一下 [+新增應用程式註冊]。
   * 名稱：`PolicyEngineProxy`
   * 應用程式類型：`Native`
   * 登入 URL：`https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` 其中`{tenantName}` 是您的 Azure AD B2C 租用戶。
1. 按一下 [建立] 。
1. 建立之後，選取應用程式 `PolicyEngineProxy`，然後複製應用程式識別碼，儲存起來供稍後使用。
1. 選取 [必要權限]，然後選取 [+新增]，再選取 [選取 API]。
1. 搜尋名稱 `PolicyEngine`，在結果中選取 PolicyEngine，然後按一下 [選取]。
1. 勾選 `Access PolicyEngine` 旁的核取方塊，然後按一下 [選取]。
1. 按一下 [完成] 。

### <a name="add-the-application-ids-to-your-custom-policy"></a>將應用程式識別碼新增至您的自訂原則

若要建立啟用本機帳戶的自訂原則，您需要將應用程式識別碼新增至擴充檔案 (`TrustFrameworkExtensions.xml`)。

1. 在擴充檔案中 (`TrustFrameworkExtensions.xml`)，尋找元素 `<TechnicalProfile Id="login-NonInteractive">`。
1. 以[您建立的原則引擎 Proxy 應用程式](#create-the-policy-engine-proxy-application)的應用程式識別碼，取代 `{Policy Engine Proxy Application ID}` 的兩個執行個體。
1. 以[您建立的原則引擎應用程式](#create-the-policy-engine-application)的應用程式識別碼，取代 `{Policy Engine Application ID}` 的兩個執行個體。
1. 儲存擴充檔案。

## <a name="upload-the-policies-to-your-tenant"></a>將原則上傳至您的租用戶

1. 在 [Azure 入口網站](https://portal.azure.com)中，[切換至您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 Azure AD B2C 刀鋒視窗。
1. 按一下 [所有原則]。
1. 選取 [上傳原則]

    >[!WARNING]
    >必須依下列順序上傳自訂原則檔︰

1. 上傳 `TrustFrameworkBase.xml`。
1. 上傳 `TrustFrameworkExtensions.xml`。
1. 上傳 `SignUpOrSignin.xml`。

上傳檔案時，名稱前面會加上 `B2C_1A_`。  內建原則是以 `B2C_1_` 開頭。

## <a name="test-the-custom-policy-using-run-now"></a>使用 [立即執行] 測試自訂原則

1. 開啟 [Azure AD B2C] 刀鋒視窗，瀏覽至 [所有原則]。
1. 選取您上傳的自訂原則，按一下 [立即執行] 按鈕。
1. 您應該可以使用電子郵件地址註冊。

## <a name="next-steps"></a>後續步驟

我們在此入門指南中所使用的基底檔案，已包含您新增其他識別提供者時需要的一些內容。 若要設定使用 Azure AD 帳戶登入，請從[這裡繼續](active-directory-b2c-setup-aad-custom.md)。

## <a name="reference"></a>參考

* **技術設定檔 (TP)** 是一個用來定義端點的名稱、其中繼資料、其通訊協定的元素，並詳細說明身分識別經驗引擎應該執行的宣告交換。  「本機帳戶登入」是身分識別經驗引擎用來執行本機帳戶登入的 TechnicalProfile。

