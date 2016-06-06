<properties
   pageTitle="在入口網站中建立 Active Directory 應用程式 | Microsoft Azure"
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
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# 使用入口網站來建立可存取資源的 Active Directory 應用程式

如果您擁有需要存取或修改資源的自動化程序或應用程式，您就必須設定一個 Active Directory 應用程式並為其指派必要的權限。本主題說明如何透過入口網站執行這些步驟。目前，您必須使用傳統入口網站來建立新的 Active Directory 應用程式，然後再切換到 Azure 入口網站，才能指派角色給該應用程式。

您有兩個可用於 Active Directory 應用程式的驗證選項︰

1. 為應用程式建立識別碼和驗證金鑰，然後在應用程式執行時提供這些認證。針對不需要使用者互動的自動化程序，請使用此選項。
2. 讓使用者能夠透過您的應用程式登入 Azure，然後使用這些認證代表使用者來存取資源。針對由使用者執行的應用程式，請使用此選項。

如需 Active Directory 概念的說明，請參閱[應用程式物件和服務主體物件](./active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

## 建立 Active Directory 應用程式

1. 透過[傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。

2. 從左窗格中，選取 [**Active Directory**]。

     ![選取 Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 選取您想要用來建立新應用程式的 Active Directory。如果您有多個 Active Directory，您通常會想在您訂用帳戶所在的目錄中建立應用程式。您只能將您訂用帳戶中資源的存取權授與和您訂用帳戶位於相同目錄的應用程式。

     ![選擇目錄](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    如果您需要尋找您訂用帳戶的目錄，請選取 [設定]，然後尋找該目錄名稱。
   
     ![尋找預設目錄](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. 若要檢視目錄中的應用程式，請按一下 [**應用程式**]。

     ![檢視應用程式](./media/resource-group-create-service-principal-portal/view-applications.png)

4. 如果您之前尚未在該目錄中建立應用程式，則應該會看到與下面類似的映像。按一下 [**新增應用程式**]。

     ![新增應用程式](./media/resource-group-create-service-principal-portal/create-application.png)

     或者，按一下下方窗格中的 [**新增**]。

     ![新增](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 選取您想要建立的應用程式類型。針對本教學課程，選取 [加入我的組織正在開發的應用程式]。

     ![新的應用程式](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. 提供應用程式的名稱，然後選取您想要建立的應用程式類型。針對本教學課程，建立 [Web 應用程式和/或 Web API]，然後按 [下一步] 按鈕。如果您選取 [原生用戶端應用程式]，則本文章中剩餘的步驟將不會與您的體驗相符。

     ![名稱應用程式](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. 填寫您應用程式的屬性。針對 [登入 URL]，提供描述您應用程式的網站 URI。這並不會驗證網站是否存在。針對 [**應用程式識別碼 URI**]，提供識別您應用程式的 URI。

     ![應用程式屬性](./media/resource-group-create-service-principal-portal/app-properties.png)

您已建立好應用程式。

## 取得用戶端識別碼和驗證金鑰

以程式設計方式登入時，您需要應用程式的識別碼。如果應用程式是在自己的認證下執行，則您還需要一個驗證金鑰。

1. 按一下 [**設定**] 索引標籤設定您應用程式的密碼。

     ![設定應用程式](./media/resource-group-create-service-principal-portal/application-configure.png)

2. 複製 [用戶端識別碼]。
  
     ![用戶端識別碼](./media/resource-group-create-service-principal-portal/client-id.png)

3. 如果應用程式將在自己的認證下執行，請向下捲動至 [金鑰] 區段，然後選取您想要的密碼有效期限。

     ![金鑰](./media/resource-group-create-service-principal-portal/create-key.png)

4. 選取 [**儲存**] 建立金鑰。

     ![儲存](./media/resource-group-create-service-principal-portal/save-icon.png)

     即會顯示儲存的金鑰，而且您可以進行複製。您將無法在稍後擷取金鑰，所以您應該會想現在就複製金鑰。

     ![儲存的金鑰](./media/resource-group-create-service-principal-portal/save-key.png)

## 取得租用戶識別碼

以程式設計方式登入時，您需要將租用戶識別碼與您的驗證要求一起傳送。針對 Web Apps 和 Web API Apps，您可以透過選取畫面底部的 [檢視端點]，然後擷取如以下所示的識別碼，來擷取租用戶識別碼。

   ![租用戶識別碼](./media/resource-group-create-service-principal-portal/save-tenant.png)

您也可以透過 PowerShell 擷取租用戶識別碼︰

    Get-AzureRmSubscription

或者，Azure CLI：

    azure account show --json

## 設定委派權限

如果應用程式代表登入使用者來存取資源，您必須對應用程式授與用來存取其他應用程式的委派權限。您可以在 [設定] 索引標籤的 [其他應用程式的權限] 區段中進行此作業。根據預設，Azure Active Directory 已啟用委派權限。請將這個委派權限保持不變。

1. 選取 [加入應用程式]。

2. 從清單中選取 [Azure 服務管理 API]。然後，選取 [完成] 圖示。

      ![選取應用程式](./media/resource-group-create-service-principal-portal/select-app.png)

3. 在委派權限下拉式清單中，選取 [以組織身分存取 Azure 服務管理]。

      ![選取權限](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 儲存變更。

## 設定多租用戶應用程式

如果其他 Azure Active Directory 的使用者可以連線到應用程式並登入，您必須啟用多重租用。在 [設定] 索引標籤上，將 [應用程式為多租用戶] 設定為 [是]。

![多租用戶](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## 將應用程式指派給角色

如果應用程式是在自己的認證下執行，您就必須將應用程式指派給某個角色。您必須決定哪些角色代表應用程式的正確權限。如要深入了解可用的角色，請參閱 [RBAC：內建角色](./active-directory/role-based-access-built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。較低的範圍層級會繼承較高層級的權限 (舉例來說，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源)。

1. 如要指派應用程式給角色，請從傳統入口網站切換到 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站中，瀏覽至您想要讓應用程式指派至的範圍層級。而對於本主題，您可以瀏覽至某個資源群組，然後選取 [資源群組] 刀鋒視窗上的**存取**圖示。

     ![選取使用者](./media/resource-group-create-service-principal-portal/select-users.png)

2. 選取 [新增]。

     ![選取 [新增]](./media/resource-group-create-service-principal-portal/select-add.png)

3. 選取 [讀取者] 角色 (或是任何您想要讓應用程式指派至的角色)。

     ![選取角色](./media/resource-group-create-service-principal-portal/select-role.png)

4. 當您第一次看到您可以新增至該角色的使用者清單時，您不會看到應用程式，只會看到群組及使用者。

     ![顯示使用者](./media/resource-group-create-service-principal-portal/show-users.png)

5. 如要尋找您的應用程式，您必須搜尋它。請開始輸入您應用程式的名稱，此時畫面上的可用選項清單會逐漸變更。當您在清單中看到您的應用程式時，請選取該應用程式。

     ![指派至角色](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. 選取 [確定] 來結束角色的指派。現在您應該會在指派至該資源群組中某個角色的清單中，看到自己的應用程式。

     ![顯示](./media/resource-group-create-service-principal-portal/show-app.png)

如需透過入口網站將使用者和應用程式指派給角色的詳細資訊，請參閱[使用 Azure 管理入口網站管理存取權](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)。

## 以程式碼取得存取權杖

您的 Active Directory 應用程式現在已設定好，可以存取資源。在您的應用程式中，您需提供認證，然後接收存取權杖。您將使用此存取權杖來要求存取資源。

您已準備好以程式設計方式登入您的應用程式。

- 如需 .NET 範例，請參閱 [Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)。
- 如需 Java 範例，請參閱 [Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)。
- 如需 Python 範例，請參閱[適用於 Python 的資源管理驗證](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)。
- 如需 REST 範例，請參閱 [Resource Manager REST API](resource-manager-rest-api.md)。

如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱[利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。

## 後續步驟

- 如要了解如何指定安全性原則，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。  
- 若要取得這些步驟的示範影片，請參閱[利用 Azure Active Directory 啟用 Azure 資源的程式化管理](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)。

<!---HONumber=AcomDC_0525_2016-->