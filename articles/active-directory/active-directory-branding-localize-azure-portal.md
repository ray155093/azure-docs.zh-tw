---
title: "在 Azure Active Directory 中將語言特定公司商標新增到登入頁面 | Microsoft Docs"
description: "了解如何將語言特定公司商標圖片和文字新增到 Azure 登入頁面"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a0310d6a-aaa7-4ea0-991d-6d3135b4382a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: e1fe8d855386ceec39edbc985538cdf32d78a13b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/05/2017


---
# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory"></a>在 Azure Active Directory 中將語言特定公司商標新增到登入頁面
為了避免混淆，許多公司都想對其管理的所有網站和服務套用一致的外觀與風格。 Azure Active Directory 是透過讓您利用公司標誌和自訂色彩配置來自訂登入頁面外觀的方式，提供這項功能。 登入頁面是當您登入 Office 365 或其他使用 Azure AD 作為識別提供者的 Web 型應用程式時，所顯示的頁面。 您可以與此頁面互動來輸入您的認證。

## <a name="customizing-the-sign-in-page-for-another-language"></a>自訂另一個語言的登入頁面
只有當您已如 [將公司商標新增到登入頁面](active-directory-branding-custom-signon-azure-portal.md)所述建立自訂登入頁面時，您才可以將語言特定元素新增到自訂登入頁面。 您可以使用一組預設的可自訂元素，為每一目錄設定一個登入頁面。 在您已設定一組預設的頁面元素之後，就可以設定不同地區設定的其他版本。 您也可以混合使用並符合各種元素。 例如，您可以：

* 建立適用於所有文化特性的預設「登入頁面影像」  ，然後建立英文和法文的特定版本。 當您將瀏覽器設定為這兩種語言其中之一時，就會顯示語言特定影像，而針對其他所有語言則會顯示預設圖例。
* 為您的組織設定不同的標誌 (例如日文或希伯來文版本)。

基於維護和效能考量，建議您不要使用太多語言變化。

**將公司商標新增到您的目錄：**

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

   ![開啟使用者管理](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. 在 [使用者和群組] 刀鋒視窗上，選取 [公司商標]。
4. 在 [使用者和群組 - 公司商標] 刀鋒視窗上，選取 [新增語言] 命令。

    ![新增語言特定商標元素](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. 修改您想要自訂的元素。 所有元素都是選用的。
6. 按一下 [儲存] 。

您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="next-steps"></a>後續步驟
[將公司商標新增到您的登入頁面](active-directory-branding-custom-signon-azure-portal.md)

