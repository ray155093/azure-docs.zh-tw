---
title: "部署、呼叫並驗證 Azure Logic Apps 的 web API 與 REST API | Microsoft Docs"
description: "在工作流程中部署、驗證並呼叫 web API 與 REST API，以便與 Azure Logic Apps 進行系統整合"
keywords: "web API, REST API, 連接器, 工作流程, 系統整合, 驗證"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: zh-tw
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>部署、呼叫並驗證自訂 API 作為 Logic Apps 的連接器

在您[建立可提供 Logic Apps 工作流程中所使用之動作或觸發程序的自訂 API](./logic-apps-create-api-app.md) 之後，您在呼叫之前必須先部署您的 API。 且雖然您可以從邏輯應用程式呼叫任何 API，但請新增描述您 API 之作業和參數的 [Swagger 中繼資料](http://swagger.io/specification/)以獲得最佳體驗。 Swagger 檔案可協助您改善 API 工作，並更輕鬆地整合 Logic Apps。

您可以將 API 部署為 [web 應用程式](../app-service-web/app-service-web-overview.md)，但請考慮將您的 API 部署為 [API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)，如此一來，當您在雲端中及內部部署建置、裝載並自訂 API 時，可讓您的作業更容易。 您不需要在 API 中變更任何程式碼 -- 只需將您的程式碼部署至 API 應用程式。 您可以在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 上裝載您的 API，Azure App Service 是平台即服務 (PaaS) 供應項目，能為 API 裝載提供最佳、最簡單且擴充性最高的方法。

若要驗證從 Logic Apps 對您 API 的呼叫，您可以在 Azure 入口網站中設定 Azure Active Directory，讓您不需要更新您的程式碼。 或者，您可以透過您的 API 程式碼要求並強制執行驗證。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>將您的 API 部署為 web 應用程式或 API 應用程式

請先將您的 API 部署為 web 應用程式或 API 應用程式到 Azure App Service 後，才能從邏輯應用程式呼叫自訂 API。 此外，若要讓 Logic App Designer 可讀取您的 Swagger 文件，請設定 API 定義屬性，並開啟 web 應用程式或 API 應用程式的[跨原始資源共用 (CORS)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig)。

1. 在 Azure 入口網站中，選取您的 Web 應用程式或 API 應用程式。

2. 在開啟的刀鋒視窗之 [API] 下方，選擇 [API 定義]。 將 [API 定義位置] 設定為 swagger.json 檔案的 URL。

   通常，URL 會以這種格式出現：`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![您自訂 API 的 Swagger 檔案連結](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. 在 [API] 下，選擇 [CORS]。 將 [允許的來源] 的 CORS 原則 設定為 **'*'** (全部允許)。

   此設定允許來自 Logic App Designer 的要求。

   ![允許來自 Logic App Designer 對您自訂 API 的要求](media/logic-apps-custom-hosted-api/custom-api-cors.png)

如需詳細資訊，請參閱這些文章：

* [新增 ASP.NET web API 的 Swagger 中繼資料](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [將 ASP.NET web API 部署至 Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>從邏輯應用程式工作流程呼叫自訂 API

在您設定 API 定義屬性和 CORS 之後，您自訂 API 的觸發程序和動作應該就可供您用來包含在邏輯應用程式工作流程中。 

*  若要檢視具有 Swagger URL 的網站，您可以在 Logic Apps Designer 中瀏覽訂用帳戶網站。

*  若要藉由指向 Swagger 文件來檢視可用的動作和輸入，請使用 [HTTP + Swagger 動作](../connectors/connectors-native-http-swagger.md)。

*  若要呼叫任何 API，包含沒有或未公開 Swagger 文件的 API，您一律可以使用 [HTTP 動作](../connectors/connectors-native-http.md)建立要求。

## <a name="authenticate-calls-to-your-custom-api"></a>驗證您自訂 API 的呼叫

您可以以下列方式保護對自訂 API 的呼叫：

* [無程式碼變更](#no-code)：透過 Azure 入口網站使用 [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) 來保護您的 API，因此您不需要更新程式碼或重新部署您的 API。

  > [!NOTE]
  > 根據預設，您在 Azure 入口網站中開啟的 Azure AD 驗證不提供精細的授權。 例如，這項驗證會將您的 API 鎖定為僅限特定租用戶，而非特定使用者或應用程式。 

* [更新您的 API 程式碼](#update-code)：保護您的 API，方法是透過程式碼強制執行[憑證驗證](#certificate)、[基本驗證](#basic)，或 [Azure AD 驗證](#azure-ad-code)。

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>驗證對 API 的呼叫，不需要變更程式碼

以下是這個方法的一般步驟：

1. 將建立兩個 [Azure Active Directory (Azure AD) 應用程式識別](../app-service-api/app-service-api-dotnet-service-principal-auth.md)：一個用於邏輯應用程式，一個用於 Web 應用程式 (或 API 應用程式)。

2. 若要驗證對您 API 的呼叫，請使用與邏輯應用程式的 Azure AD 應用程式識別碼相關聯的[服務主體](../app-service-api/app-service-api-dotnet-service-principal-auth.md)認證 (用戶端識別碼和祕密)。

3. 將應用程式識別碼包含在邏輯應用程式定義中。

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>第 1 部分：建立 Azure AD 邏輯應用程式的應用程式識別碼

您的邏輯應用程式會使用這個 Azure AD 應用程式識別碼向 Azure AD 驗證。 您只需要為您的目錄設定一次這個身分識別。 例如，儘管您可以為每個邏輯應用程式建立唯一的身分識別，但可以選擇讓所有的 Logic Apps 使用相同的身分識別。 您可以在 Azure 入口網站、[Azure 傳統入口網站](#app-identity-logic-classic)，或使用 [PowerShell](#powershell) 設定這些身分識別。

**在 Azure 入口網站中建立邏輯應用程式的應用程式識別碼**

1. 在 [Azure 入口網站](https://portal.azure.com "https://portal.azure.com")中，選擇 **Azure Active Directory**。 

2. 請確認您與 web 應用程式或 API 應用程式位於相同的目錄中。

   > [!TIP]
   > 若要切換目錄，請按一下您的設定檔並選取另一個目錄。 或者，選擇 [概觀] > [切換目錄]。

3. 在 [目錄] 功能表的 [管理] 下，選擇 [應用程式註冊] > [新增應用程式註冊]。

   > [!TIP]
   > 根據預設，應用程式註冊清單會顯示您目錄中的所有應用程式註冊。 若只要檢視您的應用程式註冊，請在搜尋方塊旁選取 [我的應用程式]。 

   ![建立新的應用程式註冊](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. 為您的應用程式識別碼指定名稱，並保留**應用程式類型**設為 **Web 應用程式 / API**，提供格式化為**登入 URL** 之網域的唯一字串，然後選擇 [建立]。

   ![提供應用程式識別碼的名稱和登入 URL](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   您針對邏輯應用程式建立的應用程式識別碼現在會出現在應用程式註冊清單中。

   ![邏輯應用程式的應用程式識別碼](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. 在應用程式註冊清單中，選取新的應用程式識別碼。 複製並儲存**應用程式識別碼**，用來作為第 3 部分中邏輯應用程式的「用戶端識別碼」。

   ![複製並儲存邏輯應用程式的應用程式識別碼](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. 如果看不到您的應用程式識別碼設定，請選擇 [設定] 或 [所有設定]。

7. 在 [API 存取] 下，選擇 [金鑰]。 在 [描述] 下，提供您金鑰的名稱。 在 [到期時間] 下，選取您金鑰的持續時間。

   您要建立的金鑰會作為應用程式識別碼的「祕密」或邏輯應用程式的密碼。

   ![建立邏輯應用程式身分識別的金鑰](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. 在工具列上，選擇 [儲存]。 在 [值] 下，現在會顯示您的金鑰。 
**請務必複製並儲存您的金鑰**供稍後使用，因為當您離開 [金鑰] 刀鋒視窗時會將金鑰隱藏。

   當您在第 3 部分設定邏輯應用程式時，您要指定這個金鑰作為「祕密」或密碼。

   ![複製並儲存金鑰供稍後使用](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**在 Azure 傳統入口網站中建立邏輯應用程式的應用程式識別碼**

1. 在 Azure 傳統入口網站中，選擇 [**[Active Directory]**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) 。

2. 選取您用於 Web 應用程式或 API 應用程式的相同目錄。

3. 在 [應用程式] 索引標籤上，選擇頁面底部的 [新增]。

4. 為您的應用程式識別碼提供名稱，然後選擇 **[下一步]** \(向右箭號)。

5. 在 [應用程式屬性] 下，提供格式化為**登入 URL** 和**應用程式識別碼 URI** 之網域的唯一字串，然後選擇 [完成] (勾選記號)。

6. 在 [設定] 索引標籤上，複製並儲存用來作為第 3 部分中邏輯應用程式的「用戶端識別碼」。

7. 在 [金鑰] 下，開啟 [選取持續時間] 清單。 選取您金鑰的持續時間。

   您要建立的金鑰會作為應用程式識別碼的「祕密」或邏輯應用程式的密碼。

8. 在頁面底部選擇 [儲存]。 您可能必須等候幾秒鐘。

9. 在 [金鑰] 下，請務必複製並儲存現在顯示的金鑰。 

   當您在第 3 部分設定邏輯應用程式時，您要指定這個金鑰作為「祕密」或密碼。

如需詳細資訊，請了解如何[設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

<a name="powershell"></a>

**在 PowerShell 中建立邏輯應用程式的應用程式識別碼**

您可以使用 PowerShell，透過 Azure Resource Manager 來執行這項工作中。 在 PowerShell 中，執行以下命令：

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. 請務必複製 [租用戶識別碼] (您 Azure AD 租用戶的 GUID)、[應用程式識別碼] 和您所使用的密碼。

如需詳細資訊，請了解如何[使用 PowerShell 建立用來存取資源的服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>第 2 部分：建立 Web 應用程式或 API 應用程式的 Azure AD 應用程式識別碼

如果已部署 web 應用程式或 API 應用程式，您就可以開啟驗證，並在 Azure 入口網站中建立應用程式識別碼。 否則，您可以[在您使用 Azure Resource Manager 範本進行部署時開啟驗證](#authen-deploy)。 

**建立應用程式識別碼，並在 Azure 入口網站中開啟已部署應用程式的驗證**

1. 在 [Azure 入口網站](https://portal.azure.com "https://portal.azure.com")中，尋找並選取您的 web 應用程式或 API 應用程式。 

2. 在 [設定] 下，選擇 [驗證/授權]。 在 [App Service 驗證] 下，將驗證 [開啟]。 在 [驗證提供者] 下，選擇 [Azure Active Directory]。

   ![開啟驗證](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. 現在建立 Web 應用程式或 API 應用程式的應用程式識別碼，如下所示。 在 [Azure Active Directory 設定] 刀鋒視窗上，將 [管理模式] 設定為 [快速]。 選擇 [建立新的 AD 應用程式]。 為您的應用程式識別碼提供名稱，然後選擇 **[確定]**。 

   ![建立 Web 應用程式或 API 應用程式的應用程式識別碼](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. 在 [驗證/授權] 刀鋒視窗上，選擇 [儲存]。

現在您必須尋找的應用程式識別碼之用戶端識別碼和租用戶識別碼，是與您的 web 應用程式或 API 應用程式相關聯。 您可以在第 3 部分中使用這些識別碼。 因此，繼續執行 Azure 入口網站或 [Azure 傳統入口網站](#find-id-classic)的下列步驟。

**在 Azure 入口網站中尋找 web 應用程式或 API 應用程式的應用程式識別碼之用戶端識別碼和租用戶識別碼**

1. 在 [驗證提供者] 下，選擇 [Azure Active Directory]。 

   ![選擇 [Azure Active Directory]](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. 在 [Azure Active Directory 設定] 刀鋒視窗上，將 [管理模式] 設定為 [進階]。

3. 複製 [用戶端識別碼]，並儲存用於第 3 部分的 GUID。

   > [!TIP] 
   > 如果未出現 [用戶端識別碼] 和 [簽發者 URL]，請嘗試重新整理 Azure 入口網站，然後重複步驟 1。

4. 在 [簽發者 URL] 下，僅複製並儲存用於第 3 部分的 GUID。 您也可以視需要在您的 web 應用程式或 API 應用程式的部署範本中使用此 GUID。

   此 GUID 是您特定租用戶的 GUID (「租用戶 ID」)，且應該會出現在此 URL：`https://sts.windows.net/{GUID}`

5. 無須儲存變更，關閉 [Azure Active Directory 設定] 刀鋒視窗。

<a name="find-id-classic"></a>

**在 Azure 傳統入口網站中尋找 web 應用程式或 API 應用程式的應用程式識別碼之用戶端識別碼和租用戶識別碼**

1. 在 Azure 傳統入口網站中，選擇 [**[Active Directory]**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) 。

2.  選取您用於 Web 應用程式或 API 應用程式的目錄。

3. 在 [搜尋] 方塊中，尋找並選取 web 應用程式或 API 應用程式的應用程式識別碼。

4. 在 [設定] 索引標籤上，複製 [用戶端識別碼]，並儲存用於第 3 部分的 GUID。

5. 取得用戶端識別碼之後，請在 [設定] 索引標籤上選擇 [檢視端點]。

6. 複製 [同盟中繼資料文件] 的 URL，並瀏覽至該 URL。

7. 在隨即開啟的中繼資料文件中，尋找根 **EntityDescriptor ID** 元素，其具有以下形式的 **entityID** 屬性：`https://sts.windows.net/{GUID}` 

      這個屬性中的 GUID 是特定租用戶的 GUID (租用戶 ID)。

8. 複製租用戶識別碼，並儲存該識別碼以在第 3 部分中使用，並視需要也在您 web 應用程式或 API 應用程式的部署範本中使用。

如需詳細資訊，請參閱下列主題：

* [Azure App Service 中 API 應用程式的使用者驗證](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Azure App Service 中的驗證與授權](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**在您使用 Azure Resource Manager 範本進行部署時開啟驗證**

您還是必須建立 web 應用程式或 API 應用程式的 Azure AD 應用程式識別碼，其與邏輯應用程式的應用程式識別碼不同。 若要建立應用程式識別碼，請遵循第 2 部分中 Azure 入口網站的先前步驟。 您也可以遵循第 1 部分中的步驟，但請務必使用您的 web 應用程式或 API 應用程式適用於**登入 URL** 和**應用程式識別碼 URI** 的實際 `https://{URL}`。 在這些步驟中，您必須儲存用戶端識別碼和租用戶識別碼，以在您應用程式的部署範本以及用於第 3 部分中使用。

> [!NOTE]
> 當您建立 web 應用程式或 API 應用程式的 Azure AD 應用程式識別碼時，必須使用 Azure 入口網站或 Azure 傳統入口網站，而不是 PowerShell。 PowerShell commandlet 不會設定使用者登入網站的必要權限。

取得用戶端識別碼和租用戶識別碼後，請在部署範本中納入這些識別碼，作為 Web 應用程式或 API 應用程式的子資源：

   ```
   "resources": [
       {
           "apiVersion": "2015-08-01",
           "name": "web",
           "type": "config",
           "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
           "properties": {
               "siteAuthEnabled": true,
               "siteAuthSettings": {
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

若要自動將空白 web 應用程式和邏輯應用程式與 Azure Active Directory 驗證共同部署，請[在這裡檢視完整範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)，或在這裡按一下 [部署至 Azure]：

[![部署至 Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>第 3 部分：填入邏輯應用程式中的授權區段

前一個範本已設定此授權區段，但如果您要直接撰寫邏輯應用程式，則必須包含完整的授權區段。

在程式碼檢視中開啟您的邏輯應用程式定義、移至 [HTTP] 動作區段、尋找 [授權] 區段，並納入這一行：

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| 元素 | 說明 |
| ------- | ----------- |
| tenant |Azure AD 租用戶的 GUID |
| audience |必要。 您想要存取之目標資源的 GUID - 來自您 web 應用程式或 API 應用程式的應用程式識別碼之用戶端識別碼 |
| clientId |要求存取之用戶端的 GUID - 來自您邏輯應用程式的應用程式識別碼之用戶端識別碼 |
| secret |必要。 來自要求存取權杖的用戶端之應用程式識別碼的金鑰或密碼 |
| 類型 |驗證類型。 若為 ActiveDirectoryOAuth 驗證，值為 `ActiveDirectoryOAuth`。 |

例如：

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>透過程式碼保護 API 呼叫

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>憑證驗證

若要驗證邏輯應用程式傳入 Web 應用程式或 API 應用程式中的要求，您可以使用用戶端憑證。 如需設定程式碼，請了解[如何設定 TLS 相互驗證](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

在 [授權] 區段中，納入這一行： 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| 元素 | 說明 |
| ------- | ----------- |
| 類型 |必要。 驗證類型。 若為 SSL 用戶端憑證，值必須是 `ClientCertificate`。 |
| password |必要。 用以存取用戶端憑證的密碼 (PFX 檔案) |
| pfx |必要。 用戶端憑證的 Base64 編碼內容 (PFX 檔案) |

<a name="basic"></a>

#### <a name="basic-authentication"></a>基本驗證

若要驗證邏輯應用程式傳入 Web 應用程式或 API 應用程式中的要求，您可以使用基本驗證，例如使用者名稱和密碼。 基本驗證的常見模式，任何用來建置 Web 應用程式或 API 應用程式的語言都可以使用此驗證。

在 [授權] 區段中，納入這一行：

`{"type": "basic", "username": "username", "password": "password"}`。

| 元素 | 說明 |
| --- | --- |
| 類型 |必要。 驗證類型。 若為基本驗證，值必須是 `Basic`。 |
| username |必要。 驗證的使用者名稱 |
| password |必要。 驗證的密碼 |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>透過程式碼驗證 Azure Active Directory

根據預設，您在 Azure 入口網站中開啟的 Azure AD 驗證不提供精細的授權。 例如，這項驗證會將您的 API 鎖定為僅限特定租用戶，而非特定使用者或應用程式。 

若要限制 API 透過程式碼存取您的邏輯應用程式，請擷取具有 JSON Web 權杖 (JWT) 的標頭。 檢查呼叫者的身分識別，並拒絕不相符的要求。

此外，若要完全在自己的程式碼中實作這項驗證，而不使用 Azure 入口網站，請了解如何[在 Azure 應用程式中使用內部部署 Active Directory 進行驗證](../app-service-web/web-sites-authentication-authorization.md)。

若要建立邏輯應用程式的應用程式識別碼，並使用此身分識別來呼叫您的 API，您必須遵循前述步驟。

## <a name="next-steps"></a>後續步驟

* [使用診斷記錄及警示檢查邏輯應用程式效能](logic-apps-monitor-your-logic-apps.md)
