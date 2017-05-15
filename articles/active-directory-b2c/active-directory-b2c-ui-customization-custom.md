---
title: "Azure Active Directory B2C：自訂原則中的 UI 自訂 | Microsoft Docs"
description: "關於在 Azure AD B2C 中使用自訂原則時自訂使用者介面 (UI) 的主題"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 215f9baeda7cd0bcf3fd66893919575647849e7d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C︰自訂原則中的 UI 自訂

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文之後，您將有一個具備您的品牌和外觀的註冊和登入自訂原則。  Azure AD B2C 讓您幾乎可完全控制呈現給使用者的 HTML 和 CSS。  使用自訂原則時，是以 XML 來設定 UI 自訂，而不是在 Azure 入口網站使用控制項。

## <a name="prerequisites"></a>必要條件

在繼續之前，您必須完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)。  您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。

### <a name="confirming-your-b2c-tenant"></a>確認您的 B2C 租用戶

由於自訂原則仍在私人預覽階段，請確認您的 Azure AD B2C 租用戶支援上傳自訂原則︰

1. 在 [Azure 入口網站](https://portal.azure.com)中，[切換至您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 Azure AD B2C 刀鋒視窗。
1. 按一下 [所有原則]。
1. 請確定 [上傳原則] 可用。  如果此按鈕已停用，請寄電子郵件到 AADB2CPreview@microsoft.com。

## <a name="the-page-ui-customization-feature"></a>頁面 UI 自訂功能

透過頁面 UI 自訂功能，您可以自訂任何自訂原則的外觀與風格。  維護您的應用程式與 Azure AD B2C 之間的品牌和視覺一致性。

以下是其運作方式：Azure AD B2C 會在取用者的瀏覽器中執行程式碼並使用稱為[跨原始資源共用 (CORS)](http://www.w3.org/TR/cors/) 的新式方法。  首先，在自訂原則中使用自訂 HTML 內容指定 URL。  Azure AD B2C 會合併 UI 元素和從您的 URL 載入的 HTML 內容，然後向取用者顯示此頁面。

## <a name="creating-your-html5-content"></a>建立您的 HTML5 內容

讓我們建立 HTML 內容並在標題中顯示您的產品品牌名稱。

1. 按一下此 html 程式碼片段的 [複製]。  它是語式正確的 HTML5，在 `<body>` 的某處有一個稱為 `<div id="api"></div>` 的空元素。 這個元素會標記插入 Azure AD B2C 內容的地方。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 在文字編輯器中貼上並儲存為檔案 `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>建立 Blob 儲存體帳戶

>[!NOTE]
> 在本指南中，我們使用 Azure Blob 儲存體來裝載內容。  您也可以將內容裝載於 Web 伺服器，但您需要[在 Web 伺服器上啟用跨原始資源共用 (CORS)](https://enable-cors.org/server.html)。

讓我們將此 HTML 裝載於 Azure Blob 儲存體。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [中樞] 功能表上，選取 [新增] -> [儲存體] -> [儲存體帳戶]。
1. 輸入儲存體帳戶的 [名稱]。
1. [部署模型] 可保持為 [Resource Manager]。
1. 將 [帳戶類型] 變更為 [Blob 儲存體]。
1. [效能] 可保持為 [標準]。
1. [複寫] 可保持為 [RA-GRS]。
1. [存取層] 可保持為 [經常性存取]。
1. [儲存體服務加密] 可保持為 [已停用]。
1. 選取儲存體帳戶的 [訂用帳戶]。
1. 建立 [資源群組]，或選取現有的資源群組。
1. 選取儲存體帳戶的 [地理位置]。
1. 按一下 [建立]  建立儲存體帳戶。
1. 等待部署完成，儲存體帳戶刀鋒視窗會自動開啟。

## <a name="create-a-container"></a>建立容器

讓我們在 Azure Blob 儲存體上建立公用容器。

1. 切換至左索引標籤 [概觀]。
1. 按一下 [+容器]。
1. 在 [名稱] 中，輸入 `$root`
1. 將 [存取類型] 設定為 [Blob]。
1. 按一下 '$root' 以開啟新的容器。
1. 按一下 [上傳] 。
1. 按一下 `Select a file` 旁的資料夾圖示。
1. 瀏覽至我們在[先前一節](#the-page-ui-customization-feature)建立的 `customize-ui.html`。
1. 按一下 [上傳] 。
1. 選取我們上傳的 blob，稱為 `customize-ui.html`。
1. 按一下 [URL] 旁的 [複製] 按鈕。
1. 開啟瀏覽器並瀏覽至這個 URL。  如果無法存取，請確定容器的存取類型設定為 blob。

## <a name="configure-cors"></a>設定 CORS

接著，我們將設定 Azure Blob 儲存體來使用跨原始資源共用 (CORS)。

>[!NOTE]
>想要使用我們的範例 HTML 和 CSS 內容來嘗試 UI 自訂功能嗎？  我們提供[簡單的協助程式工具](active-directory-b2c-reference-ui-customization-helper-tool.md)，讓您上傳我們的範例內容，並在您的 Azure Blob 儲存體帳戶上設定。  如果您使用此工具，請直接跳到[修改註冊或登入自訂原則](#modify-your-sign-up-or-sign-in-custom-policy)

1. 在 [設定] 下的 [儲存體] 刀鋒視窗中，開啟 [CORS]。
1. 按一下 [+ 新增]。
1. 將 `Allowed origins` 設定為 `*`。
1. 在下拉式清單 `Allowed verbs` 中，選取 `GET` 和 `OPTIONS`。
1. 將 `Allowed headers` 設定為 `*`。
1. 將 `Exposed headers` 設定為 `*`。
1. 將 `Maximum age (seconds)` 設定為 `200`。
1. 按一下 [新增] 。

## <a name="testing-cors"></a>測試 CORS

來驗證一下我們已經準備好。

1. 瀏覽至 http://test-cors.org/，將 URL 貼到 `Remote URL` 欄位。
1. 按一下 [傳送要求]
1. 如果您收到錯誤，請確定您的 [CORS 設定](#configure-cors)正確無誤。  您也可能需要清除瀏覽器快取，或嘗試使用 InPrivate 瀏覽工作階段 `CTRL-SHIFT-P`。

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>修改您的註冊或登入自訂原則

1. 在最上層 `<TrustFrameworkPolicy>` 標記中，您應該會發現 `<BuildingBlocks>` 標記。  在 `<BuildingBlocks>` 標記內，複製此範例來新增 `ContentDefinitions` 標記。  使用您的儲存體帳戶名稱取代 `{your_storage_account}`。

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>上傳更新的自訂原則

1. 在 [Azure 入口網站](https://portal.azure.com)中，[切換至您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 Azure AD B2C 刀鋒視窗。
1. 按一下 [所有原則]。
1. 按一下 [上傳原則]
1. 上傳 `SignUpOrSignin.xml`，其有有我們新增的 `ContentDefinitions` 標記。

## <a name="test-the-custom-policy-using-run-now"></a>使用 [立即執行] 測試自訂原則

1. 開啟 [Azure AD B2C] 刀鋒視窗，瀏覽至 [所有原則]。
1. 選取您上傳的自訂原則，按一下 [立即執行] 按鈕。
1. 您應該可以使用電子郵件地址註冊。

## <a name="next-steps"></a>後續步驟

此[內建原則 UI 自訂參考指南](active-directory-b2c-reference-ui-customization.md)包含有關可以自訂哪些 UI 元素的其他資訊。  內建原則和自訂原則的 UI 自訂沒有任何差異。

