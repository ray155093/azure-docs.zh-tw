---
title: 在 Azure Active Directory 預覽版中停用企業應用程式的使用者登入 | Microsoft Docs
description: 如何在 Azure Active Directory 中停用企業應用程式，讓任何使用者都無法登入它
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# 在 Azure Active Directory 預覽版中停用企業應用程式的使用者登入
在 Azure Active Directory (Azure AD) 預覽版中，您可以輕鬆停用企業應用程式，讓任何使用者都無法登入它。[預覽版有何功能？](active-directory-preview-explainer.md) 您必須具備適當的權限，才能管理企業應用程式。在目前的預覽版中，您必須是目錄的全域管理員。

## 如何停用使用者登入？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter**。
3. 在 [Azure Active Directory- *directoryname*] 刀鋒視窗 (也就是您所管理目錄的 Azure AD 刀鋒視窗) 上，選取 [企業應用程式]。
   
    ![開啟企業應用程式](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. 在 [企業應用程式] 刀鋒視窗上，選取 [所有應用程式]。您會看到一份您可以管理的應用程式清單。
5. 在 [企業應用程式 - 所有應用程式] 刀鋒視窗上，選取一個應用程式。
6. 在 [***appname***] 刀鋒視窗 (亦即標題中含有所選應用程式名稱的刀鋒視窗) 上，選取 [屬性]。
   
    ![選取所有應用程式命令](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. 在 [*appname* - 屬性] 刀鋒視窗上，針對 [啟用以讓使用者登入?] 選取 [否]。
8. 選取 [儲存] 命令。

## 後續步驟
* [查看我的所有群組](active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)
* [從企業應用程式中移除使用者或群組指派](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [變更企業應用程式的名稱或標誌](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->