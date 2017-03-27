---
title: "Azure Active Directory B2C：建立 Azure Active Directory B2C 租用戶 | Microsoft Docs"
description: "有關如何建立 Azure Active Directory B2C 租用戶的主題"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C：建立 Azure AD B2C 租用戶
若要開始使用 Microsoft Azure Active Directory (Azure AD) B2C，請遵循本文中所述的四個步驟。

## <a name="step-1-sign-up-for-an-azure-subscription"></a>步驟 1：註冊 Azure 訂用帳戶
如果您已經有 Azure 訂用帳戶，請略過此步驟。 如果沒有，請註冊 [Azure 訂用帳戶](../active-directory/sign-up-organization.md) 並存取 Azure AD B2C。

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>步驟 2：建立 Azure AD B2C 租用戶
請使用下列步驟，建立新的 Azure AD B2C 租用戶。 目前您無法在現有租用戶中開啟 B2C 功能。

1. 以訂用帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。 此為您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的 Microsoft 帳戶。
2. 按一下 [新增] (或已收合的 + 按鈕)，然後在 [搜尋 Marketplace] 欄位中輸入 "Azure Active Directory B2C"。
   
    ![尋找 Azure AD B2C 選項的螢幕擷取畫面](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. 在結果中，按一下 [Azure Active Directory B2C]。

    ![Azure Active Directory B2C 結果的螢幕擷取畫面](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. 將會出現含有 B2C 詳細資料的頁面。  按一下底部的 [建立]，開始設定新的 Azure Active Directory B2C 租用戶。
5. 按一下 [建立新的 Azure AD B2C 租用戶]。
6. 為您的租用戶選擇 [組織名稱]、[網域名稱] 和 [國家或區域]。

    ![建立新租用戶的表單螢幕擷取畫面](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. 按一下 [建立] 以建立租用戶。  這可能需要幾分鐘的時間，完成時，您會在通知中收到通知。

8. 您的租用戶現會建立，且將會出現在 Active Directory 擴充功能中。 系統亦會將您設為租用戶的「全域管理員」。 您可視需要新增其他「全域管理員」。
   
   > [!IMPORTANT]
   > 如果您打算將 B2C 租用戶用於生產應用程式，請閱讀 [生產級別租用戶與預覽 B2C 租用戶](active-directory-b2c-reference-tenant-type.md)一文。 請注意，當您刪除現有的 B2C 租用戶並使用相同的網域名稱加以重建時，會發生已知的問題。 您必須使用不同的網域名稱建立 B2C 租用戶。
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>步驟 3：瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗
1. 展開左邊導覽列下方的 [更多服務]。
2. 搜尋 **Azure AD B2C**，然後按一下結果 (可以將此加入我的最愛，方便日後存取)。

    ![在瀏覽窗格中搜尋 Azure AD B2C 的螢幕擷取畫面](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. 系統會在新的瀏覽器索引標籤或視窗中，開啟顯示 B2C 功能刀鋒視窗的 Azure 入口網站。
   
4. 將此刀鋒視窗釘選至「開始面板」，以方便您存取 (功能刀鋒視窗右上角的 [釘選] 工具)。
   
    ![B2C 功能刀鋒視窗和釘選按鈕的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>步驟 4：將您的 Azure AD B2C 租用戶連結到 Azure 訂用帳戶
如果您打算將您的 B2C 租用戶用於生產環境應用程式，您將必須將 Azure AD B2C 租用戶連結到您的 Azure 訂用帳戶以支付使用費用。 若要了解做法，請參閱[這篇文章](active-directory-b2c-how-to-enable-billing.md)。

   > [!IMPORTANT]
   > 如果您未將 Azure AD B2C 租用戶連結到您的 Azure 訂用帳戶，您將會在 Azure 入口網站的 [B2C 功能] 刀鋒視窗上看到警告訊息 (「沒有訂用帳戶與此 B2C 租用戶連結，或訂用帳戶需要您的注意。」)。 請務必先進行此步驟，然後才將您的應用程式傳送到生產環境。
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>輕鬆存取 Azure 入口網站上的 B2C 功能刀鋒視窗
為了改善探索能力，我們已在 Azure 入口網站新增 B2C 功能刀鋒視窗的捷徑。

1. 以 B2C 租用戶的全域系統管理員身分登入 Azure 入口網站。 如果您已登入不同租用戶，請切換租用戶 (在右上角)。
2. 在左側導覽中按一下 [瀏覽]  。
3. 按一下 [Azure AD B2C]  以存取 B2C 功能刀鋒視窗。
   
    ![瀏覽至 B2C 功能刀鋒視窗的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>後續步驟
請閱讀 [Azure Active Directory B2C：註冊您的應用程式](active-directory-b2c-app-registration.md)，了解如何使用 Azure AD B2C 註冊應用程式，以及建置快速啟動應用程式。


