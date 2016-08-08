<properties
	pageTitle="Azure Active Directory B2C：建立 Azure Active Directory B2C 租用戶 | Microsoft Azure"
	description="有關如何建立 Azure Active Directory B2C 租用戶的主題"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.topic="article"
    ms.devlang="na"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C：建立 Azure AD B2C 租用戶

若要開始使用 Microsoft Azure Active Directory (Azure AD) B2C，請遵循本文中所述的三個步驟。

## 步驟 1：註冊 Azure 訂用帳戶

如果您已經有 Azure 訂用帳戶，請略過此步驟。如果沒有，請註冊 [Azure 訂用帳戶](../active-directory/sign-up-organization.md)並存取 Azure AD B2C。

## 步驟 2：建立 Azure AD B2C 租用戶

請使用下列步驟，建立新的 Azure AD B2C 租用戶。目前您無法在現有租用戶中開啟 B2C 功能。

1. 以訂用帳戶管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。此為您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的 Microsoft 帳戶。
2. 依序按一下 [新增] > [應用程式服務] > [Active Directory] > [目錄] > [自訂建立]。

    ![開始建立租用戶的螢幕擷取畫面](./media/active-directory-b2c-get-started/new-directory.png)

3. 為您的租用戶選擇 [名稱]、[網域名稱] 和 [國家或區域]。
4. 勾選顯示**這是 B2C 目錄**的選項。
5. 按一下核取記號以完成動作。

    ![建立 B2C 目錄的核取記號螢幕擷取畫面](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 您的租用戶現會建立，且將會出現在 Active Directory 擴充功能中。系統亦會將您設為租用戶的「全域管理員」。您可視需要新增其他「全域管理員」。

    > [AZURE.IMPORTANT]
	如果您打算將 B2C 租用戶用於生產應用程式，請閱讀[生產級別租用戶與預覽 B2C 租用戶](active-directory-b2c-reference-tenant-type.md)一文。

## 步驟 3：瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗

1. 瀏覽至位於左側導覽列的 Active Directory 擴充。
2. 在 [目錄] 索引標籤下方，尋找並按一下租用戶。
3. 按一下 [設定] 索引標籤。
4. 按一下 [B2C 管理] 區段中的 [管理 B2C 設定] 連結。

    ![B2C 目錄組態的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. 系統會在新的瀏覽器索引標籤或視窗中，開啟顯示 B2C 功能刀鋒視窗的 Azure 入口網站。

    > [AZURE.IMPORTANT]
    最多可能需要 2 到 3 分鐘的時間，才能在 Azure 入口網站上存取租用戶。稍待一會兒後再重試這些步驟，即可修正此問題。否則請連絡支援人員。

6. 將此刀鋒視窗釘選至「開始面板」，以方便您存取(釘選工具會在右上角的功能刀鋒視窗中標示為紅色)。

    ![B2C 功能刀鋒視窗的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上，管理租用戶的使用者和群組、自助式密碼重設設定，以及公司商標功能。

## 輕鬆存取 Azure 入口網站上的 B2C 功能刀鋒視窗

為了改善探索能力，我們已在 Azure 入口網站新增 B2C 功能刀鋒視窗的捷徑。

1. 以 B2C 租用戶的全域系統管理員身分登入 Azure 入口網站。如果您已登入不同租用戶，請切換租用戶 (在右上角)。
2. 在左側導覽中按一下 [瀏覽]。
3. 按一下 [Azure AD B2C] 以存取 B2C 功能刀鋒視窗。

    ![瀏覽至 B2C 功能刀鋒視窗的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-browse.png)

## 後續步驟

請閱讀 [Azure Active Directory B2C：註冊您的應用程式](active-directory-b2c-app-registration.md)，了解如何使用 Azure AD B2C 註冊應用程式，以及建置快速啟動應用程式。

<!---HONumber=AcomDC_0727_2016-->