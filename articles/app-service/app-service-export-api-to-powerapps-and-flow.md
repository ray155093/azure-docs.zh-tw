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
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef
ms.lasthandoff: 02/10/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>將 Azure 裝載 API 匯出至 PowerApps 和 Microsoft Flow

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>為 PowerApps 和 Microsoft Flow 建立自訂 API

[Microsoft 商務應用程式平台](https://businessplatform.microsoft.com/)包含各種可協助使用者執行更多作業的產品。 [PowerApps](https://powerapps.com) 是一種服務，可用來建置和使用自訂商務 App，以便跨越平台連接到您的資料和工作。 [Microsoft Flow](https://flow.microsoft.com) 可讓您輕易將最愛的應用程式與服務之間的工作流程和商業程序自動化。 PowerApps 和 Microsoft Flow 隨附資料來源 (如 Office 365、Dynamics 365、Salesforce 等等) 的各種內建連接器。 不過，使用者也必須能夠利用其組織所建置的資料來源和 API。

同樣地，想要在組織內更廣泛地公開其 API 的開發人員，可以讓 PowerApps 和 Microsoft Flow 使用者使用其 API。 本主題將說明如何向 PowerApps 和 Microsoft Flow 公開使用 Azure App Service 或 Azure Functions 建置的 API。 [Azure App Service](https://azure.microsoft.com/services/app-service/) 是一項平台即服務方案供應項目，可讓開發人員快速且輕鬆地建置企業級 Web、行動及 API 應用程式。 [Azure Functions](https://azure.microsoft.com/services/functions/) 是以事件為基礎的無伺服器計算解決方案，可讓您快速撰寫程式碼來回應您系統的其他部分並根據需求進行調整。

若要深入了解這些服務，請參閱︰
- [PowerApps 引導式學習](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow 引導式學習](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [什麼是 App Service？](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [什麼是 Azure Functions？](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>共用 API 定義

API 通常使用 [Open API 文件](https://www.openapis.org/) (有時稱為 "Swagger" 文件) 加以描述。 這包含所有關於哪些作業可用以及如何組織資料的資訊。 PowerApps 和 Microsoft Flow 可以為任何 Open API 2.0 文件建立自訂 API。 建立自訂 API 後，即可用與其中一個內建連接器完全相同的方式加以使用，並可快速地整合到應用程式中。

Azure App Service 和 Azure Functions 有[內建支援](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)，可供建立、裝載及管理 Open API 文件。 若要為 Web、行動、API 或函式應用程式建立自訂連接器，必須執行兩個步驟︰

1. [從 App Service 或 Azure Functions 擷取 API 定義](#export)
2. [將 API 定義匯入 PowerApps 中](#import)

這兩個步驟可能必須由組織內的不同人員執行，因為指定的使用者可能不具備執行這兩個動作的權限。 在此情況下，擁有 App Service 或 Azure Functions 應用程式參與者權限的開發人員必須取得 API 定義 (單一 JSON 檔案) 或其連結。 他們必須接著將該定義提供給 PowerApps 或 Microsoft Flow 擁有者。 該擁有者可以使用中繼資料來建立自訂 API。

> [!NOTE]
> 因為正在使用 API 定義的複本，所以 PowerApps 和 Microsoft Flow 不會立即知道應用程式的相關更新或重大變更。 如果可以使用新版的 API，應針對新版本重複這些步驟。 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>從 App Service 或 Azure Functions 擷取 API 定義

在這一節中，您將匯出 App Service API 的 API 定義，以便稍後使用於 PowerApps。

1. 開啟 [Azure 入口網站](https://portal.azure.com)並瀏覽至 App Service 或 Azure Functions 應用程式。

    如果使用 Azure App Service，請從設定清單選取 [API 定義]。 
    
    如果使用 Azure Functions，請選取 [函式應用程式設定]，然後選取 [設定 API 中繼資料]。

2. 如果已提供 API 定義，您會看到 [匯出至 PowerApps + Microsoft Flow] 按鈕。 按一下此按鈕，開始匯出程序。

3. 您可以選擇**下載 API 定義**或**取得連結**。 不管您選擇何者，都會在下一節中將結果提供給 PowerApps。 選取其中一個選項並遵循指示。
 
4. 如果您的 API 定義包含任何安全性定義，則會在步驟 2 中叫出這些定義。 在匯入期間，PowerApps 和 Microsoft Flow 會偵測這些定義並提示輸入安全性資訊。 服務會使用此資訊來登入使用者，使其能夠存取 API。 如果您的 API 需要驗證，請確保它會在 Open API 文件中擷取為_安全性定義_。

    收集與每個定義相關的認證，以便使用於下一節中。 如需 PowerApps 原本支援的識別提供者清單和各自需要的認證，請參閱[在 PowerApps 中註冊自訂 API] 和[在 Microsoft Flow 中註冊自訂 API]。
 
> [!NOTE]
> 如果使用 Azure Active Directory 驗證，則需要新的 AAD 應用程式註冊，其已委派您的 API 和回覆 URL (_https://msmanaged-na.consent.azure-apim.net/redirect_) 的存取權。 請參閱[本範例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)以取得詳細資訊，並以您的 API 取代 Azure Resource Manager。
>
> 如果另一個人要將 API 定義匯入 PowerApps 中，您將提供**新註冊**的用戶端識別碼和用戶端密碼，以及您 API 的資源 URL (API 定義檔除外)。 確定這些密碼都已受到安全地管理。 **請勿共用 API 本身的安全性認證。**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>將 API 定義匯入 PowerApps 和 Microsoft Flow 中

在本節中，您將使用稍早取得的 API 定義，在 PowerApps 和 Microsoft Flow 中建立自訂 API。 自訂 API 會由這兩項服務共用，因此您只需要匯入定義一次。 如需有關自訂 API 的詳細資訊，請參閱[在 PowerApps 中註冊自訂 API] 和[在 Microsoft Flow 中註冊自訂 API]。

**若要匯入至 PowerApps：**

1. 開啟 [PowerApps Web 入口網站](https://web.powerapps.com)，登入，然後選取 [連線]。 按一下 [新增連線]。

2. 選取 [自訂]，然後按一下 [新增自訂 API]。

3. 提供 API 的名稱，然後上傳 Swagger 定義或貼在中繼資料 URL 中。 按 [下一步] 。

4. 如果系統提示您提供驗證詳細資訊，請輸入在上一節中取得的值。 如果沒有，請繼續進行下一個步驟。

5. 按一下 [建立] 。

**若要匯入至 Microsoft Flow：**

1. 開啟 [Microsoft Flow Web 入口網站](https://flow.microsoft.com/)並登入。 

2. 按一下頁面右上方的 [設定] 按鈕 (看起來像齒輪)，然後選取 [自訂 API]。 按一下 [建立自訂 API]。

3. 上傳 Swagger 定義，然後按一下 [繼續]。

4. 如果系統提示您提供驗證詳細資訊，請輸入在上一節中取得的值。 如果沒有，請繼續進行下一個步驟。

5. 按一下畫面頂端的核取方塊。



[在 PowerApps 中註冊自訂 API]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[在 Microsoft Flow 中註冊自訂 API]: https://flow.microsoft.com/documentation/register-custom-api/

