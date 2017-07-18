---
title: "將 Azure 裝載 API 匯出至 PowerApps 和 Microsoft Flow | Microsoft Docs"
description: "如何向 PowerApps 和 Microsoft Flow 公開 App Service 中裝載之 API 的概觀"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>將 Azure 裝載 API 匯出至 PowerApps 和 Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>為 PowerApps 和 Microsoft Flow 建立自訂連接器

[PowerApps](https://powerapps.com) 是一種服務，可用來建置和使用自訂商務 App，以便跨越平台連接到您的資料和工作。 [Microsoft Flow](https://flow.microsoft.com) 可讓您輕易將最愛的應用程式與服務之間的工作流程和商業程序自動化。 PowerApps 和 Microsoft Flow 隨附資料來源 (如 Office 365、Dynamics 365、Salesforce 等等) 的各種內建連接器。 不過，使用者也必須能夠利用其組織所建置的資料來源和 API。

同樣地，想要在組織內更廣泛地公開其 API 的開發人員，可以讓 PowerApps 和 Microsoft Flow 使用者使用其 API。 本主題將說明如何向 PowerApps 和 Microsoft Flow 公開使用 Azure App Service 或 Azure Functions 建置的 API。 [Azure App Service](https://azure.microsoft.com/services/app-service/) 是一項平台即服務方案供應項目，可讓開發人員快速且輕鬆地建置企業級 Web、行動及 API 應用程式。 [Azure Functions](https://azure.microsoft.com/services/functions/) 是以事件為基礎的無伺服器計算解決方案，可讓您快速撰寫程式碼來回應您系統的其他部分並根據需求進行調整。

若要深入了解這些服務，請參閱︰
- [PowerApps 引導式學習](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 引導式學習](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [什麼是 App Service？](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [什麼是 Azure Functions？](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>共用 API 定義

API 通常使用 [OpenAPI 文件](https://www.openapis.org/) (有時稱為 "Swagger" 文件) 來描述。 這包含所有關於哪些作業可用以及如何組織資料的資訊。 PowerApps 和 Microsoft Flow 可以為任何 OpenAPI 2.0 文件建立自訂連接器。 建立自訂連接器後，即可用與其中一個內建連接器完全相同的方式加以使用，並可快速地整合到應用程式中。

Azure App Service 和 Azure Functions 有[內建支援](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)，可供建立、裝載及管理 OpenAPI 文件。 若要建立適用於 Web、行動、API 或函數應用程式的自訂連接器，則必須提供定義的複本給 PowerApps 和 Flow。

> [!NOTE]
> 因為正在使用 API 定義的複本，所以 PowerApps 和 Microsoft Flow 不會立即知道應用程式的相關更新或重大變更。 如果可以使用新版的 API，應針對新版本重複這些步驟。 

若要將您應用程式的裝載 API 定義提供給 PowerApps 和 Microsoft Flow，請依照這些步驟執行：

1. 開啟 [Azure 入口網站](https://portal.azure.com)並瀏覽至 App Service 或 Azure Functions 應用程式。

    如果使用 Azure App Service，請從設定清單選取 [API 定義]。 
    
    如果使用 Azure Functions，請選取您的函數應用程式，然後依序選擇 [平台功能]、[API 定義]。 您也可以改為選擇開啟 [API 定義 (預覽)] 索引標籤。

2. 如果已提供 API 定義，您會看到 [匯出至 PowerApps + Microsoft Flow] 按鈕。 按一下此按鈕，開始匯出程序。

3. 選取 [匯出模式]。 這會決定您建立連接器時將要遵循的步驟。 App Service 提供兩種選項，供您將 API 定義提供給 PowerApps 和 Microsoft Flow：

    [快速] 可讓您從 Azure 入口網站建立自訂連接器。 它要求目前登入的使用者必須有在目標環境中建立連接器的權限。 如果可符合該需求，則這是建議的方法。 如果使用此模式，請依照下面的[快速匯出](#express)指示執行。

    [手動] 可讓您匯出 API 定義的複本，該複本可使用 PowerApps 或 Microsoft Flow 入口網站匯入。 如果 Azure 使用者和具有建立連接器權限的使用者是不同的人，或連接器必須在另一個租用戶中建立，則這是建議的方法。 如果使用此模式，請依照下面的[手動匯出和匯入](#manual)指示執行。

<a name="express"></a>
## <a name="express-export"></a>快速匯出

在本節中，您會從 Azure 入口網站建立新的自訂連接器。 您必須登入要作為匯出目標的租用戶，且必須有在目標環境中建立自訂連接器的權限。

1. 選取您要在其中建立連接器的環境。 然後提供您的自訂連接器名稱。

2. 如果您的 API 定義包含任何安全性定義，則會在步驟 2 中叫出這些定義。 必要時，請提供授與使用者存取 API 權限所需的安全性設定詳細資料。 如需詳細資訊，請參閱下面的[驗證](#auth)。 

3. 按一下 [確定] 以建立自訂連接器。


<a name="manual"></a>
## <a name="manual-export-and-import"></a>手動匯出和匯入

若要為 Web、行動、API 或函式應用程式建立自訂連接器，必須執行兩個步驟︰

1. [從 App Service 或 Azure Functions 擷取 API 定義](#export)
2. [將 API 定義匯入到 PowerApps 和 Microsoft Flow](#import)

這兩個步驟可能必須由組織內的不同人員執行，因為指定的使用者可能不具備執行這兩個動作的權限。 在此情況下，擁有 App Service 或 Azure Functions 應用程式參與者權限的開發人員必須取得 API 定義 (單一 JSON 檔案) 或其連結。 他們必須接著將該定義提供給 PowerApps 或 Microsoft Flow 擁有者。 該擁有者可以使用中繼資料來建立自訂連接器。

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>從 App Service 或 Azure Functions 擷取 API 定義

在本節中，您將匯出 App Service API 的 API 定義，以便稍後使用於 PowerApps 或 Microsoft Flow 入口網站。

1. 您可以選擇**下載 API 定義**或**取得連結**。 不管您選擇何者，都會在下一節中提供結果。 選取其中一個選項並遵循指示。
 
2. 如果您的 API 定義包含任何安全性定義，則會在步驟 2 中叫出這些定義。 在匯入期間，PowerApps 和 Microsoft Flow 會偵測這些定義並提示輸入安全性資訊。 收集與每個定義相關的認證，以便使用於下一節中。 如需詳細資訊，請參閱下面的[驗證](#auth)。 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>將 API 定義匯入 PowerApps 和 Microsoft Flow 中

在本節中，您將使用稍早取得的 API 定義，在 PowerApps 和 Microsoft Flow 中建立自訂連接器。 自訂連接器會由這兩項服務共用，因此您只需要匯入定義一次。 如需有關自訂連接器的詳細資訊，請參閱[在 PowerApps 中註冊及使用自訂連接器]和[在 Microsoft Flow 中註冊及使用自訂連接器]。

1. 開啟 [Powerapps Web 入口網站](https://web.powerapps.com)或 [Microsoft Flow Web 入口網站](https://flow.microsoft.com/)，並登入。 

2. 按一下頁面右上角的 [設定] 按鈕 (齒輪圖示)，然後選取 [自訂連接器]。 

3. 按一下 [建立自訂連接器]。

4. 在 [一般] 索引標籤上提供您 API 的名稱，然後上傳 OpenAPI 定義或貼到中繼資料 URL 中。 按一下 [繼續]。

4. 在 [安全性] 索引標籤上，如果系統提示您提供驗證詳細資訊，請輸入在上一節中取得的值。 如果沒有，請繼續進行下一個步驟。

5. 在 [定義] 索引標籤上，會自動填入在 OpenAPI 檔案定義的所有作業。 如果所有的必要作業皆已定義，您便可以前往下個步驟。 如果沒有，您可以在此新增與修改作業。

6. 按一下 [建立連接器]。 如果您想要測試 API 呼叫，請移至下一個步驟。

7. 在 [測試] 索引標籤上，建立連接、選取作業，然後輸入作業所需的任何資料。

8. 按一下 [測試作業]。


<a name="auth"></a>
## <a name="authentication"></a>驗證

PowerApps 和 Microsoft Flow 原生支援識別提供者集合，這可用來登入您自訂連接器的使用者。 如果您的 API 需要驗證，請確保它會依照您 OpenAPI 文件中的_安全性定義_擷取。 在匯出期間，您必須提供設定值，允許 PowerApps 和 Microsoft Flow 執行登入動作。

本節涵蓋的驗證類型是快速流程所支援的類型：API 金鑰、Azure Active Directory 和 Generic OAuth 2.0。 如需提供者和個別所需之認證的完整清單，請參閱[在 PowerApps 中註冊及使用自訂連接器]和[在 Microsoft Flow 中註冊及使用自訂連接器]。

### <a name="api-key"></a>API 金鑰
若使用此安全性配置，當連接器的使用者建立連線時，系統會提示他們提供金鑰。 您可以提供 API 金鑰名稱，協助他們了解所需的金鑰為何。 針對 Azure Functions，這通常是涵蓋函數應用程式內許多功能的其中一個主機金鑰。

### <a name="azure-active-directory"></a>Azure Active Directory
當設定需要 AAD 登入的自訂連接器時，需要兩個 AAD 應用程式註冊：一個用於建立後端 API 模型，另一個用於建立 PowerApps 和 Flow 中連接器的模型。

您的 API 應該設定為搭配初次註冊使用，如果您使用 [App Service 驗證/授權](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)功能，則已經設定完成。

您將必須使用[新增 AAD 應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)中所涵蓋的步驟，手動建立連接器的第二個註冊。 註冊必須具有 API 和 `https://msmanaged-na.consent.azure-apim.net/redirect` 之回覆 URL 的委派存取權。 請參閱[此範例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)以取得詳細資訊，並以您的 API 取代 Azure Resource Manager。

下列設定值是必要的：
- **用戶端識別碼**：您連接器 AAD 註冊的用戶端識別碼
- **用戶端祕密**：您連接器 AAD 註冊的用戶端祕密
- **登入 URL**：AAD 的基底 URL。 在公用 Azure 中，這通常是 `https://login.windows.net`。
- **租用戶識別碼**：要用於登入的租用戶識別碼。 這應該是 "common"，或建立連接器所在之租用戶的識別碼。
- **資源 URL**：API 後端 AAD 註冊的資源 URL

> [!IMPORTANT]
> 如果另一個人會將 API 定義匯入到 PowerApps 和 Microsoft Flow 作為手動流程的一部分，您將需要提供**連接器註冊**的用戶端識別碼和用戶端祕密，以及 API 的資源 URL。 確定這些密碼都已受到安全地管理。 **請勿共用 API 本身的安全性認證。**

### <a name="generic-oauth-20"></a>Generic OAuth 2.0
Generic OAuth 2.0 支援可讓您與任何 OAuth 2.0 提供者整合。 這可讓您引入任何原生不支援的自訂提供者。

下列設定值是必要的：
- **用戶端識別碼**：OAuth 2.0 用戶端識別碼
- **用戶端祕密**：OAuth 2.0 用戶端祕密
- **授權 URL**：OAuth 2.0 授權 URL
- **權杖 URL**：OAuth 2.0 權杖 URL
- **重新整理 URL**：OAuth 2.0 重新整理 URL



[在 PowerApps 中註冊及使用自訂連接器]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[在 Microsoft Flow 中註冊及使用自訂連接器]: https://flow.microsoft.com/documentation/register-custom-api/

