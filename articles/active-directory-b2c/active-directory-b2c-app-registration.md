---
title: "Azure Active Directory B2C：應用程式註冊 | Microsoft Docs"
description: "如何向 Azure Active Directory B2C 註冊您的應用程式"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C：註冊您的應用程式

> [!IMPORTANT]
> 在 Azure 入口網站中從 [Azure AD B2C] 刀鋒視窗建立的應用程式，必須從相同的位置進行管理。 如果您使用 PowerShell 或其他入口網站編輯 B2C 應用程式，系統會不支援這些應用程式，這些應用程式可能也不適用於 Azure AD B2C。
> 
> 

## <a name="prerequisite"></a>先決條件
若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。 使用 [建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來建立您自己的租用戶。 在您遵循該文章中的所有步驟之後，B2C 功能刀鋒視窗應會釘選至您的「開始面板」。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>瀏覽至 B2C 功能刀鋒視窗
若您已將 B2C 功能刀鋒視窗釘選至「開始面板」，則在您以 B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/) 時，即會看見該刀鋒視窗。

您也可以在 [Azure 入口網站](https://portal.azure.com/)的左側導覽窗格中按一下 [更多服務]，然後搜尋 [Azure AD B2C]，來存取刀鋒視窗。

> [!IMPORTANT]
> 您必須是 B2C 租用戶的全域管理員，才能存取 B2C 功能刀鋒視窗。 其他租用戶的全域管理員或所有租用戶的使用者均無法存取。  您可以使用 Azure 入口網站右上角中的租用戶切換器來切換至 B2C 租用戶。
> 
> 

## <a name="register-a-web-application"></a>註冊 Web 應用程式
1. 在 Azure 入口網站的 B2C 功能刀鋒視窗中，按一下 [應用程式] 。
2. 按一下刀鋒視窗頂端的 [新增]  。
3. 輸入應用程式的 **名稱** ，此名稱將會為取用者說明您的應用程式。 例如，您可以輸入「Contoso B2C app」。
4. 將 [包括 web 應用程式 / web API] 切換為 [是]。 **回覆 URL** 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。 例如，輸入 `https://localhost:44316/`。
5. 按一下 [建立]  以註冊您的應用程式。
6. 按一下您剛才建立的應用程式，並複製稍後要在程式碼中使用的全域唯一 **應用程式用戶端識別碼** 。 
7. 若您的 Web 應用程式也會呼叫 Azure AD B2C 所保護的 Web API，您可移至 [金鑰] 刀鋒視窗並按一下 [產生金鑰] 按鈕，以建立 [應用程式密碼]。

> [!NOTE]
> **應用程式密碼** 是重要的安全性認證，應該適當地加以保護。
> 
   

## <a name="register-a-web-api"></a>註冊 web API
1. 在 Azure 入口網站的 B2C 功能刀鋒視窗中，按一下 [應用程式] 。
2. 按一下刀鋒視窗頂端的 [新增]  。
3. 輸入應用程式的 **名稱** ，此名稱將會為取用者說明您的應用程式。 例如，您可以輸入「Contoso B2C api」。
4. 將 [包括 web 應用程式 / web API] 切換為 [是]。 **回覆 URL** 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。 例如，輸入 `https://localhost:44316/`。
5. 輸入 [應用程式識別碼 URI]。 這是您的 Web API 所使用的識別碼。 例如，輸入 'notes'。 它會在下方產生完整識別碼 URI。 
6. 按一下 [建立]  以註冊您的應用程式。
7. 按一下您剛才建立的應用程式，並複製稍後要在程式碼中使用的全域唯一 **應用程式用戶端識別碼** 。
8. 按一下 [已發佈範圍]。 您會在此定義可授予其他應用程式的權限 (範圍)。
9. 視需要新增更多範圍。 根據預設，將會定義 "user_impersonation" 範圍。 這讓其他應用程式能夠代表已登入的使用者存取此 API。 如果您希望，可加以移除。 
10. 按一下 [儲存] 。

## <a name="register-a-mobilenative-application"></a>註冊行動/原生應用程式
1. 在 Azure 入口網站的 B2C 功能刀鋒視窗中，按一下 [應用程式] 。
2. 按一下刀鋒視窗頂端的 [新增]  。
3. 輸入應用程式的 **名稱** ，此名稱將會為取用者說明您的應用程式。 例如，您可以輸入「Contoso B2C app」。
4. 將 [包含原生用戶端] 切換為 [是]。
5. 輸入**重新導向 URI** 與自訂配置。 例如，com.onmicrosoft.contoso.appname://redirect/path。 請確定您選擇 [良好重新導向 URI](#choosing-a-redirect-uri)。
6. 按一下 [儲存] 以註冊您的應用程式。
7. 按一下您剛才建立的應用程式，並複製稍後要在程式碼中使用的全域唯一 **應用程式用戶端識別碼** 。
8. 若您的原生應用程式也會呼叫 Azure AD B2C 所保護的 Web API，您可移至 [金鑰] 刀鋒視窗並按一下 [產生金鑰] 按鈕，以建立 [應用程式密碼]。

> [!NOTE]
> **應用程式密碼** 是重要的安全性認證，應該適當地加以保護。
> 

### <a name="choosing-a-redirect-uri"></a>選擇 [重新導向 URI]
選擇行動/原生應用程式的重新導向 URI 時，有兩個重要的考量︰ 
* **唯一**︰每個應用程式的重新導向 URI 的配置都應該是唯一。 在我們的範例 (com.onmicrosoft.contoso.appname://redirect/path) 中，我們會使用 com.onmicrosoft.contoso.appname 做為配置。 我們建議您遵循此模式。 如果兩個應用程式共用相同的配置，使用者會看到「選擇應用程式」的對話方塊。 如果使用者選擇不正確，登入將會失敗。 
* **完成**︰重新導向 URI 必須有配置和路徑。 路徑必須在網域之後包含至少一個正斜線 (例如，//contoso/ 可使用，而 //contoso 會失敗)。 

## <a name="build-a-quick-start-application"></a>建置快速啟動應用程式
您現已在 Azure AD B2C 註冊應用程式，只需完成其中一個快速啟動教學課程，即可開始操作和執行。 以下是一些建議：

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


