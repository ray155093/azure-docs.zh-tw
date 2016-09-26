<properties
   pageTitle="在入口網站中建立服務主體 | Microsoft Azure"
   description="描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# 使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)


如果您擁有需要存取或修改資源的應用程式，您就必須設定一個 Active Directory (AD) 應用程式並為其指派必要的權限。本主題說明如何透過入口網站執行這些步驟。目前，您必須使用傳統入口網站來建立新的 Active Directory 應用程式，然後再切換到 Azure 入口網站，才能指派角色給該應用程式。

> [AZURE.NOTE] 您可能會發現您可以透過 [PowerShell](resource-group-authenticate-service-principal.md) 或 [Azure CLI](resource-group-authenticate-service-principal-cli.md) 輕鬆設定 AD 應用程式和服務主體，尤其是如果您想要使用憑證進行驗證時。本主題不會說明如何使用憑證。

如需 Active Directory 概念的說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

## 建立 Active Directory 應用程式

1. 透過[傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。

2. 請確定您知道訂用帳戶的預設 Active Directory。您只能將存取權授與和您訂用帳戶位於相同目錄的應用程式。選取 [設定]，然後尋找與您訂用帳戶關聯的目錄名稱。如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)。
   
     ![尋找預設目錄](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. 從左窗格中，選取 [**Active Directory**]。

     ![選取 Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 選取您想要用來建立應用程式的 Active Directory。如果您有多個 Active Directory，請在您訂用帳戶的預設目錄中建立應用程式。

     ![選擇目錄](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. 若要檢視目錄中的應用程式，選取 [應用程式]。

     ![檢視應用程式](./media/resource-group-create-service-principal-portal/view-applications.png)

4. 如果您之前尚未在該目錄中建立應用程式，則應該會看到類似下圖。選取 [新增應用程式]

     ![新增應用程式](./media/resource-group-create-service-principal-portal/create-application.png)

     或者，按一下下方窗格中的 [**新增**]。

     ![新增](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 選取您想要建立的應用程式類型。針對本教學課程，選取 [新增我的組織正在開發的應用程式]。

     ![新的應用程式](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. 提供應用程式的名稱，然後選取您想要建立的應用程式類型。針對本教學課程，建立 [Web 應用程式和/或 Web API]，然後按 [下一步] 按鈕。如果您選取 [原生用戶端應用程式]，則本文章中剩餘的步驟將不會與您的體驗相符。

     ![名稱應用程式](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. 填寫您應用程式的屬性。在 [登入 URL] 提供描述您應用程式之網站的 URI。這並不會驗證網站是否存在。針對 [**應用程式識別碼 URI**]，提供識別您應用程式的 URI。

     ![應用程式屬性](./media/resource-group-create-service-principal-portal/app-properties.png)

您已建立好應用程式。

## 取得用戶端識別碼和驗證金鑰

以程式設計方式登入時，您需要應用程式的識別碼。如果應用程式是在自己的認證下執行，則您還需要一個驗證金鑰。

1. 按一下 [設定] 索引標籤設定應用程式的密碼。

     ![設定應用程式](./media/resource-group-create-service-principal-portal/application-configure.png)

2. 複製 [用戶端識別碼]。
  
     ![用戶端識別碼](./media/resource-group-create-service-principal-portal/client-id.png)

3. 如果應用程式將在自己的認證下執行，請向下捲動至 [金鑰] 區段，然後選取您想要的密碼有效期限。

     ![金鑰](./media/resource-group-create-service-principal-portal/create-key.png)

4. 選取 [**儲存**] 建立金鑰。

     ![儲存](./media/resource-group-create-service-principal-portal/save-icon.png)

     即會顯示儲存的金鑰，而且您可以進行複製。您無法在之後擷取金鑰，所以您現在就要複製金鑰。

     ![儲存的金鑰](./media/resource-group-create-service-principal-portal/save-key.png)

## 取得租用戶識別碼

以程式設計方式登入時，您需要將租用戶識別碼與您的驗證要求一起傳送。針對 Web Apps 和 Web API Apps，您可以透過選取畫面底部的 [檢視端點]，然後擷取如下圖中所示的識別碼，來擷取租用戶識別碼。

   ![租用戶識別碼](./media/resource-group-create-service-principal-portal/save-tenant.png)

您也可以透過 PowerShell 擷取租用戶識別碼︰

    Get-AzureRmSubscription

或者，Azure CLI：

    azure account show --json

## 設定委派權限

如果應用程式代表登入使用者來存取資源，您必須對應用程式授與用來存取其他應用程式的委派權限。您可以在 [設定] 索引標籤的 [其他應用程式的權限] 區段中進行此作業。根據預設，Azure Active Directory 已啟用委派權限。請將這個委派權限保持不變。

1. 選取 [新增應用程式]。

2. 在清單中選取 [Windows Azure 服務管理 API]。然後，選取 [完成] 圖示。

      ![選取應用程式](./media/resource-group-create-service-principal-portal/select-app.png)

3. 在委派權限下拉式清單中，選取 [以組織身分存取 Azure 服務管理]。

      ![選取權限](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 儲存變更。

## 將應用程式指派給角色

如果應用程式是在自己的認證下執行，您就必須將應用程式指派給某個角色。決定哪個角色代表應用程式的正確權限。如要深入了解可用的角色，請參閱 [RBAC：內建角色](./active-directory/role-based-access-built-in-roles.md)。

若要將角色指派給應用程式，您必須使用正確的權限。具體而言，您必須擁有 `Microsoft.Authorization/*/Write` 存取權，而這是透過[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。參與者角色並沒有正確的存取權。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。較低的範圍層級會繼承較高層級的權限。例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 如要指派應用程式給角色，請從傳統入口網站切換到 [Azure 入口網站](https://portal.azure.com)。

1. 檢查您的權限，確定您可以將服務主體指派給角色。選取您的帳戶的 [我的權限]。

    ![選取我的權限](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. 檢視指派給您的帳戶的權限。如先前所述，您必須是「擁有者」或「使用者存取系統管理員」角色，或是具有可授與 Microsoft.Authorization 寫入存取權的自訂角色。下圖顯示指派給訂用帳戶的「參與者」角色的帳戶，它沒有足夠權限可指派應用程式給角色。

    ![顯示選取我的權限](./media/resource-group-create-service-principal-portal/show-permissions.png)

     如果您沒有授與應用程式存取權的正確權限，您必須要求您的訂用帳戶系統管理員將您新增至「使用者存取系統管理員」角色，或要求系統管理員授與您應用程式的存取權。

1. 瀏覽至您想要讓應用程式指派至的範圍層級。若要指派訂用帳戶範圍的角色，請選取 [訂用帳戶]。

     ![選取訂用帳戶](./media/resource-group-create-service-principal-portal/select-subscription.png)

     選取指派應用程式時作為對象的特定訂用帳戶。

     ![選取要指派的訂用帳戶](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     選取右上角的 [存取] 圖示。

     ![選取存取](./media/resource-group-create-service-principal-portal/select-access.png)
     
     或者，若要指派資源群組範圍的角色，請瀏覽至資源群組。從 [資源群組] 刀鋒視窗中，選取 [存取控制]。

     ![選取使用者](./media/resource-group-create-service-principal-portal/select-users.png)

     以下是就所有範圍而言都相同的步驟。

2. 選取 [新增]。

     ![選取 [新增]](./media/resource-group-create-service-principal-portal/select-add.png)

3. 選取 [讀取者] 角色 (或是任何您想要讓應用程式指派至的角色)。

     ![選取角色](./media/resource-group-create-service-principal-portal/select-role.png)

4. 當您第一次看到您可以新增至該角色的使用者清單時，您不會看到應用程式，只會看到群組及使用者。

     ![顯示使用者](./media/resource-group-create-service-principal-portal/show-users.png)

5. 如要尋找您的應用程式，您必須搜尋它。請開始輸入您應用程式的名稱，此時畫面上的可用選項清單會逐漸變更。當您在清單中看到您的應用程式時，請選取該應用程式。

     ![指派至角色](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. 選取 [確定] 來結束角色的指派。現在您應該會在指派至該資源群組中某個角色的清單中，看到自己的應用程式。


如需透過入口網站將使用者和應用程式指派給角色的詳細資訊，請參閱[使用角色指派來管理 Azure 訂用帳戶資源的存取權](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal)。

## 範例應用程式

下列範例應用程式會顯示如何登入為服務主體。

**.NET**

- [使用 .NET 以範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [使用 .NET 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [開始使用資源 - 在 Java 中使用 Azure Resource Manager 範本進行部署](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [開始使用資源 - 在 Java 中管理資源群組](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [使用 Python 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [使用 Python 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [使用 Node.js 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [使用 Node.js 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [使用 Ruby 格式的範本部署已啟用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [使用 Ruby 管理 Azure 資源和資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## 後續步驟

- 如要了解如何指定安全性原則，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。
- 若要取得這些步驟的示範影片，請參閱[利用 Azure Active Directory 啟用 Azure 資源的程式化管理](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)。

<!---HONumber=AcomDC_0914_2016-->