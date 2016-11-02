<properties
    pageTitle="搭配使用 Office 365 租用戶與 Azure 訂用帳戶 | Microsoft Azure"
    description="了解如何將 Office 365 目錄 (租用戶) 加入至 Azure 訂用帳戶，以便建立關聯。"
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>


# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>將 Office 365 租用戶與 Azure 訂用帳戶產生關聯
如果您在過去分開取得 Azure 和 Office 365 訂用帳戶，而且現在想要能夠從 Azure 訂用帳戶存取 Office 365 租用戶。很簡單。 本文將說明如何做到。

> [AZURE.NOTE] 本文不適用於 Enterprise 合約 (EA) 客戶。

## <a name="quick-guidance"></a>簡要指引
若要將 Office 365 租用戶與您的 Azure 訂用帳戶建立關聯，請使用您的 Azure 帳戶來新增 Office 365 租用戶，然後將您的 Azure 訂用帳戶與 Office 365 租用戶建立關聯。

## <a name="detailed-steps"></a>詳細步驟
在此案例中，Kelley Wall 這名使用者的 Azure 訂用帳戶位於 kelley.wall@outlook.com. 帳戶之下。Kelley 也擁有 Office 365 訂用帳戶，其位於 kelley.wall@contoso.onmicrosoft.com. 帳戶之下。現在 Kelley 想要透過 Azure 訂用帳戶存取 Office 365 租用戶。

![Azure Active Directory 狀態的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Office 365 系統管理中心作用中使用者的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>必要條件
若要讓關聯正確運作，您必須滿足以下必要條件︰

- 您需要 Azure 訂用帳戶的服務管理員認證。 共同管理員無法執行一部分的步驟。
- 您需要 Office 365 租用戶的全域系統管理員認證。
- 服務管理員的電子郵件地址不得包含於 Office 365 租用戶中。
- 服務管理員的電子郵件地址不得符合 Office 365 租用戶的任何全域系統管理員。
- 如果您目前使用的電子郵件地址同時為 Microsoft 帳戶和組織帳戶，請暫時將 Azure 訂用帳戶的服務管理員變更成使用另一個 Microsoft 帳戶。 您可以在 [Microsoft 帳戶註冊頁面](https://signup.live.com/)建立新的 Microsoft 帳戶。


若要變更您的服務管理員，請遵循下列步驟：

1. 登入[帳戶管理入口網站](https://account.windowsazure.com/subscriptions)。
2. 選取您想變更的訂用帳戶。
3. 選取 [編輯訂用帳戶詳細資料]。

    ![Azure 訂用帳戶資訊的螢幕擷取畫面，其中 [編輯訂用帳戶詳細資料] 已反白顯示](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. 在 [服務管理員]  方塊中，輸入新的服務管理員的電子郵件地址。

    ![[編輯訂用帳戶] 對話方塊的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>將 Office 365 租用戶與 Azure 訂用帳戶產生關聯
若要將 Office 365 租用戶與 Azure 訂用帳戶產生關聯，請遵循下列步驟︰

1.  使用服務管理員認證登入[帳戶管理入口網站](https://account.windowsazure.com/subscriptions)。
2.  在左窗格中選取 [ACTIVE DIRECTORY]。

    ![Active Directory 項目的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] 您應該不會看見 Office 365 租用戶。 如果您看見，請略過下一個步驟。

    ![Azure Active Directory 預設目錄的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. 將 Office 365 租用戶加入至您的 Azure 訂用帳戶。

    a. 選取 [新增] > 目錄] > 自訂建立]。

    ![Azure Active Directory 自訂建立的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. 在 [新增目錄] 頁面中，於 [目錄] 下方選取 [使用現有的目錄]。 接著選取 [我現在已經可以登出]，然後選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

    ![[使用現有的目錄] 螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. 在您登出之後，使用 Office 365 租用戶的全域系統管理員認證登入。

    ![Office 365 全域系統管理員登入的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. 選取 [繼續]。

    ![驗證的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. 按一下 [立即登出]。

    ![登出的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. 使用服務管理員認證登入[帳戶管理入口網站](https://account.windowsazure.com/subscriptions)。

    ![Azure 登入的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. 您應該會在儀表板中看到 Office 365 租用戶。

    ![儀表板的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. 請變更與 Azure 訂用帳戶相關聯的目錄。

    a. 選取 [Settings] (設定) 。

    ![Azure 傳統入口網站設定圖示的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. 選取您的 Azure 訂用帳戶，然後 [編輯目錄]。
    ![Azure 訂用帳戶編輯目錄的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. 選取 [下一步] ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。

    ![[變更相關聯的目錄] 螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] 您會收到警告，表示將會移除所有的共同管理員。

    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] 此外，在現有資源群組中具有指派存取權的所有 [角色型存取控制 (RBAC)](./active-directory/role-based-access-control-configure.md) 使用者也會一併移除。 不過，您收到的警告只提及共同管理員的移除。

    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. 選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

5. 您現在可以將您的 Office 365 組織帳戶新增為 Azure Active Directory 租用戶的共同管理員。

    a. 選取 [系統管理員] 索引標籤，然後選取 [新增]。

    ![Azure 傳統入口網站設定系統管理員索引標籤的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. 輸入您的 Office 365 租用戶組織帳戶，選取 Azure 訂用帳戶，然後選取 [完成] ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

    ![Azure 新增共同管理員對話方塊的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. 返回 [系統管理員] 索引標籤。 您應該會看到組織帳戶顯示為共同管理員。

    ![系統管理員索引標籤的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. 接下來，您可以測試共同管理員的存取。

    a. 登出 [帳戶管理入口網站]。

    b. 開啟[帳戶管理入口網站](https://account.windowsazure.com/subscriptions)或 [Azure 入口網站](https://portal.azure.com/)。

    c. 如果 Azure 登入頁面有 [使用您的組織帳戶登入] 連結，請選取此連結。 否則，請略過此步驟。

    ![Azure 登入頁面的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. 輸入共同管理員的認證，然後選取 [登入] 。

    ![Azure 登入頁面的螢幕擷取畫面](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>後續步驟
相關案例包括：

- 您已經擁有 Office 365 訂用帳戶，並且已做好 Azure 訂用帳戶的準備，不過您想要將現有的 Office 365 使用者帳戶用於 Azure 訂用帳戶。
- 您是 Azure 訂閱者，並且想要為現有 Azure Active Directory 執行個體中的使用者取得 Office 365 訂用帳戶。

若要了解如何完成這些工作，請參閱 [搭配使用現有的 Office 365 帳戶與 Azure 訂用帳戶，反之亦然](billing-use-existing-office-365-account-azure-subscription.md)。



<!--HONumber=Oct16_HO2-->


