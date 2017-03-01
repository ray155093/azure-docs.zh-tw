---
title: "在 Azure Logic Apps 中呼叫自訂 API | Microsoft Docs"
description: "使用 Azure Logic Apps 呼叫裝載於 Azure App Service 的自訂 API"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>使用 Azure Logic Apps 呼叫裝載於 Azure App Service 的自訂 API

雖然 Azure Logic Apps 為各種服務提供 40 個以上的連接器，但您可能會想要呼叫自訂 API 以執行自己的程式碼。 Azure App Service 提供一種最簡單又最可調整的方法來裝載您自己的自訂 Web API。 本文說明如何呼叫裝載在 App Service API 應用程式、Web 應用程式或行動裝置應用程式中的任何 Web API。
了解[如何建置 API 做為邏輯應用程式中的觸發程序或動作](../logic-apps/logic-apps-create-api-app.md)。

## <a name="deploy-your-web-app"></a>部署 Web 應用程式

首先，您必須將 API 部署為 Azure App Service 中的 Web 應用程式。 了解[建立 ASP.NET Web 應用程式時的基本部署](../app-service-web/web-sites-dotnet-get-started.md)。 雖然您可以從邏輯應用程式呼叫任何 API，但是為了取得最佳體驗，我們建議您加入 Swagger 中繼資料，以便輕易地與邏輯應用程式動作整合。 了解[新增 Swagger 中繼資料](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)。

### <a name="api-settings"></a>API settings

為了讓邏輯應用程式設計工具剖析您的 Swagger，您必須啟用 CORS，並設定 Web 應用程式的 API 定義屬性。 

1.    在 Azure 入口網站中，選取您的 Web 應用程式。
2.    在開啟的刀鋒視窗中，找出 **API**，然後選取 [API 定義]。 將 [API 定義位置] 設定為 swagger.json 檔案的 URL。

    此 URL 通常是 https://{name}.azurewebsites.net/swagger/docs/v1)。

3.    若要允許來自邏輯應用程式設計工具的要求，請在 [API] 下選取 [CORS]，並設定 '*' 的 CORS 原則。

## <a name="call-into-your-custom-api"></a>呼叫自訂 API

如果您設定 CORS 和 API 定義屬性，您應該能夠輕鬆地在 Logic Apps 入口網站中，將自訂 API 動作新增至工作流程。 在 Logic Apps 設計工具中，您可以瀏覽訂用帳戶網站，以列出已定義 Swagger URL 的網站。 您也可以指向 Swagger，使用 HTTP + Swagger 動作列出可用的動作和輸入。 最後，您永遠可以使用 HTTP 動作建立要求來呼叫任何 API，即使是沒有或未公開 Swagger 文件的 API 也一樣。

有幾種不同的方式可保護 API：

*    不需要變更程式碼。 您可以使用 Azure Active Directory 來保護 API，完全不需要變更程式碼或重新部署。
*    在 API 的程式碼中，強制執行基本驗證、Azure Active Directory 驗證或憑證驗證。

## <a name="secure-calls-to-your-api-without-changing-code"></a>保護對 API 的呼叫，不需要變更程式碼

在本節中，您將建立兩個 Azure Active Directory 應用程式 – 一個用於邏輯應用程式，一個用於 Web 應用程式。 使用與邏輯應用程式的 Azure Active Directory 應用程式相關聯的服務主體 (用戶端識別碼和密碼)，驗證對 Web 應用程式的呼叫。 最後，將應用程式識別碼加入邏輯應用程式定義中。

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>第 1 部分：設定邏輯應用程式的應用程式身分識別碼

您的邏輯應用程式會使用這個應用程式識別向 Azure Active Directory 驗證。 您只需要為您的目錄設定一次這個身分識別。 例如，儘管您可以為每個邏輯應用程式建立唯一的身分識別，您也可以選擇讓您所有的邏輯應用程式使用相同的身分識別。 您可以在 Azure 入口網站或使用 PowerShell 設定這些身分識別。

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站建立應用程式身分識別

1. 在 Azure 傳統入口網站中，移至 [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)。 
2. 選取您用於 Web 應用程式的目錄。
3. 選擇 [應用程式] 索引標籤。 在頁面底部的命令列中，選擇 [新增]。
5. 為您的應用程式身分識別指定名稱，按 [下一步] 箭號。
6. 在 [應用程式屬性] 下，以網域格式輸入唯一字串，然後按一下核取記號。
7. 選擇 [設定] 索引標籤。 移至 [用戶端識別碼]，複製用戶端識別碼供邏輯應用程式中使用。
8. 在 [金鑰] 下，開啟 [選取持續時間] 清單，選取金鑰的持續時間。
9. 在頁面底部按一下 [儲存]。 您可能必須等候幾秒鐘。
10. 請務必複製現在出現於 [金鑰] 下方的金鑰，以便在邏輯應用程式中使用此金鑰。

#### <a name="create-the-application-identity-using-powershell"></a>使用 PowerShell 建立應用程式身分識別碼

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 請務必複製 [租用戶識別碼]、[應用程式識別碼] 和您所使用的密碼。

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>第 2 部分：使用 Azure Active Directory 應用程式身分識別保護 Web 應用程式

如果 Web 應用程式已部署，您可以在 Azure 入口網站中啟用授權。 否則，您可以在 Azure Resource Manager 部署時啟用授權。

#### <a name="enable-authorization-in-the-azure-portal"></a>在 Azure 入口網站中啟用授權

1. 尋找並選取您的 Web 應用程式。 在 [設定] 下，選擇 [驗證/授權]。
2. 在 [App Service 驗證] 下，**開啟**驗證，然後選擇 [儲存]。

此時，系統會自動為您建立應用程式。 在第 3 部分需要此應用程式的用戶端識別碼，因此您必須依照下列步驟進行：

1. 在 Azure 傳統入口網站中，移至 [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)。
2.    選取您的目錄。
3. 在搜尋方塊中，尋找您的應用程式。
4. 在清單中，選取您的應用程式。
5. 選擇 [設定] 索引標籤，您在這裡應該會看到 [用戶端識別碼]。

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本部署 Web 應用程式

首先，您必須為 Web 應用程式建立應用程式，這不同於您用於邏輯應用程式的應用程式。 首先請遵循第 1 部分中的前述步驟，但在 **HomePage** 和 **IdentifierUris**，中，請使用 Web 應用程式的實際 https://**URL**。

> [!NOTE]
> 當您為 Web 應用程式建立應用程式時，您必須使用 [Azure 傳統入口網站](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)。 PowerShell commandlet 不會設定使用者登入網站的必要權限。

取得用戶端識別碼和租用戶識別碼後，請在部署範本中納入這個部分做為 Web 應用程式的子資源：

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
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

若要自動執行部署，以同時部署使用 Azure Active Directory 的空白 Web 應用程式和邏輯應用程式，請按一下 [部署至 Azure]：

[![部署至 Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

如需完整範本，請參閱[以邏輯應用程式呼叫裝載於 App Service 上且受到 Azure Active Directory 保護的自訂 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>第 3 部分：填入邏輯應用程式中的授權區段

在 [HTTP] 動作的 [授權] 區段中：

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 元素 | 說明 |
| ------- | ----------- |
| 類型 |驗證類型。 若為 ActiveDirectoryOAuth 驗證，值為 `ActiveDirectoryOAuth`。 |
| tenant |用來識別 AD 租用戶的租用戶識別碼。 |
| audience |必要。 您要連接到的資源。 |
| clientID |Azure AD 應用程式的用戶端識別碼。 |
| secret |必要。 要求權杖之用戶端的密碼。 |

前一個範本已設定此授權區段，但如果您要直接撰寫邏輯應用程式，則必須包含完整的授權區段。

## <a name="secure-your-api-in-code"></a>在程式碼中保護 API

### <a name="certificate-authentication"></a>憑證驗證

您可以使用用戶端憑證來驗證傳入 Web 應用程式中的要求。 有關如何設定程式碼，請參閱[如何設定 Web 應用程式的 TLS 相互驗證](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

[授權] 區段中應該包含： 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| 元素 | 說明 |
| ------- | ----------- |
| 類型 |必要。 驗證類型。 若為 SSL 用戶端憑證，值必須是 `ClientCertificate`。 |
| pfx |必要。 Base64 編碼的 PFX 檔案內容。 |
| password |必要。 存取 PFX 檔案的密碼。 |

### <a name="basic-authentication"></a>基本驗證

若要驗證傳入要求，您可以使用基本驗證，例如使用者名稱和密碼。 基本驗證的常見模式，任何用來建置應用程式的語言都可以使用此驗證。

[授權] 區段中應該包含：

`{"type": "basic","username": "test","password": "test"}`。

| 元素 | 說明 |
| --- | --- |
| 類型 |必要。 驗證類型。 若為基本驗證，值必須是 `Basic`。 |
| username |必要。 要驗證的使用者名稱。 |
| password |必要。 要驗證的密碼。 |

### <a name="handle-azure-active-directory-authentication-in-code"></a>在程式碼中處理 Azure Active Directory 驗證

根據預設，您在 Azure 入口網站中啟用的 Azure Active Directory 驗證不提供精細的授權。 例如，此驗證不會將您的 API 鎖定給特定使用者或應用程式，而只是鎖定給特定租用戶。

例如，若要在程式碼中將 API 限制給邏輯應用程式，請擷取含有 JWT 的標頭。 檢查呼叫者的身分識別，並拒絕不相符的要求。

此外，若要完全在自己的程式碼中實作這項驗證，而不使用 Azure 入口網站功能，請參閱[在 Azure 應用程式中使用內部部署 Active Directory 進行驗證](../app-service-web/web-sites-authentication-authorization.md)。
若要建立邏輯應用程式的應用程式身分識別，並使用此身分識別來呼叫您的 API，您必須遵循前述步驟。
