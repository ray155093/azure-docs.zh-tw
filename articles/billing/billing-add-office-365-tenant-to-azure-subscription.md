---
title: "搭配使用 Office 365 租用戶與 Azure 訂用帳戶 | Microsoft Docs"
description: "了解如何將 Office 365 目錄 (租用戶) 加入至 Azure 訂用帳戶。"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.lasthandoff: 02/22/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>將 Office 365 租用戶與 Azure 訂用帳戶產生關聯
連結個別的 Azure 和 Office 365 訂用帳戶，這樣您就可以從 Azure 訂用帳戶存取 Office 365 租用戶。 若要連結您的訂用帳戶，請使用 Azure 服務系統管理員帳戶登入 Azure，新增一個目錄，然後將 Office 365 組織帳戶新增到 Azure Active Directory 租用戶。

如果您要讓 Azure Active Directory 執行個體中的使用者擁有 Office 365 訂用帳戶，或您有 Office 365 帳戶但沒有 Azure 帳戶，請參閱[使用 Office 365 帳戶註冊 Azure](billing-use-existing-office-365-account-azure-subscription.md)。 

## <a name="before-you-begin"></a>開始之前
* 您必須有 Azure 訂用帳戶服務的系統管理員認證。 共同管理員無法執行本文中某些步驟。 若要變更服務管理員，請參閱[如何新增或變更 Azure 管理員角色](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)。
* 您必須有 Office 365 租用戶的全域系統管理員認證。
* 服務管理員的電子郵件地址不得位於 Office 365 租用戶中。
* 服務管理員的電子郵件地址不得符合 Office 365 租用戶的任何全域系統管理員。
* 如果您使用的電子郵件地址同時為 Microsoft 帳戶和組織帳戶，請暫時將 Azure 訂用帳戶的服務管理員變更成使用另一個 Microsoft 帳戶。 您可以在 [Microsoft 帳戶註冊頁面](https://signup.live.com/)建立新的 Microsoft 帳戶。

## <a name="link-office-365-tenant-to-azure-subscription"></a>將 Office 365 租用戶連結到 Azure 訂用帳戶
若要將 Office 365 租用戶與 Azure 訂用帳戶產生關聯，請遵循下列步驟︰

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>步驟 1：將 Office 365 租用戶加入至 Azure 訂用帳戶

1. 使用服務管理員認證登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

    ![Azure 登入的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. 在左窗格中選取 [ACTIVE DIRECTORY]。 您應該不會看見 Office 365 租用戶。 如果有看見租用戶，請跳至[步驟 2：變更與 Azure 訂用帳戶相關聯的目錄](#Step2)。
   
   ![Active Directory 項目的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. 選取 新增 > 目錄 > 自訂建立。
   
    ![Azure Active Directory 自訂建立的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. 在 [新增目錄] 頁面中，於 [目錄] 下方選取 [使用現有的目錄]。 接著選取 [我現在已經可以登出]，然後選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![[使用現有的目錄] 螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. 在您登出之後，使用 Office 365 租用戶的全域系統管理員認證登入。
   
    ![Office 365 全域系統管理員登入的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. 選取 [繼續]。
   
    ![驗證的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. 按一下 [立即登出]。
   
    ![登出的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. 使用服務管理員認證登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
   
    ![Azure 登入的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. 您應該會在儀表板中看到 Office 365 租用戶。
   
    ![儀表板的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="a-namestep2astep-2-change-the-directory-associated-with-the-azure-subscription"></a><a name="Step2"></a>步驟 2：變更與 Azure 訂用帳戶相關聯的目錄
   
1. 選取 [Settings] (設定) 。
   
    ![Azure 傳統入口網站設定圖示的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. 選取您的 Azure 訂用帳戶，然後 [編輯目錄]。

    ![Azure 訂用帳戶編輯目錄的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. 選取 [下一步] ![下一步圖示](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。
   
    ![[變更相關聯的目錄] 螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. 檢閱受影響的帳戶。 現有資源群組中所有具有指派存取權的共同管理員和[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)使用者也會一併移除。 您收到的警告只會提及共同管理員的移除。
      
    ![顯示將移除之共同管理員帳戶的螢幕擷取畫面。](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![顯示將移除之範例使用者帳戶的螢幕擷取畫面。](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. 選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>步驟 3：將您的 Office 365 組織帳戶新增為 Azure Active Directory 租用戶的共同管理員
   
1. 選取 [系統管理員] 索引標籤，然後選取 [新增]。
   
    ![Azure 傳統入口網站設定系統管理員索引標籤的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. 輸入您的 Office 365 租用戶組織帳戶，選取 Azure 訂用帳戶，然後選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![Azure 新增共同管理員對話方塊的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. 返回 [系統管理員] 索引標籤。 您應該會看到組織帳戶顯示為共同管理員。
   
    ![系統管理員索引標籤的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  測試使用共同管理員帳戶存取 Azure。
   
    a. 登出 Azure 傳統入口網站。
   
    b.這是另一個 C# 主控台應用程式。 開啟 [Azure 入口網站](https://portal.azure.com/)。
   
    c. 輸入共同管理員的認證，然後選取 [登入] 。
   
    ![Azure 登入頁面的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。



