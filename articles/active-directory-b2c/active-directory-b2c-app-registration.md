<properties
	pageTitle="Azure Active Directory B2C：應用程式註冊 | Microsoft Azure"
	description="如何向 Azure Active Directory B2C 註冊您的應用程式"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/22/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C：註冊您的應用程式

## 必要條件

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。使用[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來建立您自己的租用戶。在您遵循該文章中的所有步驟之後，B2C 功能刀鋒視窗應會釘選至您的「開始面板」。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 瀏覽至 B2C 功能刀鋒視窗

若您已將 B2C 功能刀鋒視窗釘選至「開始面板」，則在您以 B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)時，即會看見該刀鋒視窗。

您也可以在 [Azure 入口網站](https://portal.azure.com/)的左側功能窗格中依序按一下 [瀏覽] 和 [Azure AD B2C]，來存取刀鋒視窗。

> [AZURE.IMPORTANT] 您必須是 B2C 租用戶的全域管理員，才能存取 B2C 功能刀鋒視窗。其他租用戶的全域管理員或所有租用戶的使用者均無法存取。您可以使用 Azure 入口網站右上角中的租用戶切換器來切換至 B2C 租用戶。

## 註冊應用程式

1. 在 Azure 入口網站的 B2C 功能刀鋒視窗中，按一下 [應用程式]。
2. 按一下刀鋒視窗頂端的 [新增]。
3. 輸入應用程式的**名稱**，此名稱將會為取用者說明您的應用程式。例如，您可以輸入「Contoso B2C app」。
4. 如果您正在撰寫 Web 應用程式，請將 [包含 Web 應用程式/Web API] 切換為 [是]。**回覆 URL** 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。例如，輸入 `https://localhost:44321/`。若您的 Web 應用程式也會呼叫 Azure AD B2C 所保護的一些 Web API，您可按一下 [產生金鑰] 按鈕以建立 [應用程式密碼]。
5. 如果您正在撰寫行動應用程式，請將 [包含原生用戶端] 切換為 [是]。複製系統自動為您建立的預設**重新導向 URI**。
6. 按一下 [建立] 以註冊您的應用程式。
7. 按一下您剛才建立的應用程式，並複製稍後要在程式碼中使用的全域唯一**應用程式用戶端識別碼**。

> [AZURE.NOTE] **應用程式密碼**是重要的安全性認證，應該適當地加以保護。

## 建置快速啟動應用程式

您現已在 Azure AD B2C 註冊應用程式，只需完成其中一個快速啟動教學課程，即可開始操作和執行。以下是一些建議：

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0727_2016-->