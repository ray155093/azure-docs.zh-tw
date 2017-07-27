---
title: "Azure Active Directory B2C：要求存取權杖 | Microsoft Docs"
description: "本文將說明如何設定用戶端應用程式，並取得存取權杖。"
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 7a28c92e921354cd4a623af29098a40e1c8b56b0
ms.contentlocale: zh-tw
ms.lasthandoff: 06/07/2017


---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C︰要求存取權杖

存取權杖 (表示為**存取\_權杖**) 是一種安全性權杖形式，用戶端可用來存取受[授權伺服器](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)保護的資源，例如 web API。 存取權杖統稱為[JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens)，且包含關於預期資源伺服器和伺服器授與權限的資訊。 在呼叫資源伺服器時，存取權杖必須出現在 HTTP 要求中。

本文將討論如何設定用戶端應用程式，並讓它提出要求以從 `authorize` 和 `token` 端點取得**存取\_權杖**。

> [!NOTE]
> **Azure AD B2C 不支援 Web API 鏈結 (代理者)。**
>
> 許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。 此情況常見於有 Web API 後端的原生用戶端，而後端會再呼叫 Azure AD 圖形 API 等 Microsoft 線上服務。
>
> 使用 OAuth 2.0 Jwt 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="prerequisite"></a>必要條件

要求存取權杖之前，您必須先註冊 web API 和發佈可以授與用戶端應用程式的權限。 遵循[註冊 web API](active-directory-b2c-app-registration.md#register-a-web-api) 一節下的步驟開始使用。

## <a name="granting-permissions-to-a-web-api"></a>授與權限給 web API

為了讓用戶端應用程式取得 API 的特定權限，必須透過 Azure 入口網站授與這些權限給用戶端應用程式。 若要授與權限給用戶端應用程式︰

1. 瀏覽至 B2C 功能刀鋒視窗中的**應用程式**功能表。
1. 如果您還沒有用戶端應用程式 ([Web 應用程式](active-directory-b2c-app-registration.md#register-a-web-application)或[原生用戶端](active-directory-b2c-app-registration.md#register-a-mobilenative-application))，請註冊一個。
1. 在應用程式的 [設定] 刀鋒視窗上，選取 [API 存取權]。
1. 按一下 [新增]。
1. 選取您的 web API，以及您想要授與的範圍 (權限)。
1. 按一下 [確定] 。

> [!NOTE]
> Azure AD B2C 不會要求您的用戶端應用程式使用者同意。 相反地，根據上述應用程式之間設定的權限，所有同意都係由系統管理員提供。 如果已撤銷應用程式的權限授與，所有先前能夠取得該權限的使用者將不再能夠執行這項操作。

## <a name="requesting-a-token"></a>要求權杖

若要取得資源應用程式的存取權杖，用戶端應用程式必須指定要求的**範圍**參數中想要權限。 例如，若要取得具備資源應用程式 (其應用程式識別碼 URI 為 `https://contoso.onmicrosoft.com/notes`) 之「讀取」權限，則範圍會是 `https://contoso.onmicrosoft.com/notes/read`。 以下是對 `authorize` 端點授權碼要求的範例。

> [!NOTE]
> 此時，自訂網域並未和存取權杖一起受到支援。 您必須在要求 URL 中使用您的 yourtenantId.onmicrosoft.com 網域。

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

要在相同的要求中取得多個權限，您可以在單一**範圍**參數中新增多個項目，以空格分隔。 例如：

解碼的 URL：

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

編碼的 URL：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

您可能會要求比授與用戶端應用程式更多的資源範圍/權限。 在此情況時，如果至少授與一個權限，則呼叫會成功。 所產生的**存取\_權杖**會僅使用已成功授與的權限填入其 “scp” 宣告。

> [!NOTE] 
> 我們在相同的要求中不支援兩個不同 web 資源要求的權限。 這種要求將會失敗。

### <a name="special-cases"></a>特殊案例

OpenID Connect 標準會指定數個特殊的「範圍」值。 下列特殊範圍代表「存取使用者設定檔」的權限︰

* **openid**︰這會要求識別碼權杖
* **離線\_存取**︰這會要求重新整理權杖 (使用授權碼流程)。

如果 `authorize` 要求中的「回應\_類型」參數包含「權杖」、「範圍」參數必須至少包含一個會授與的資源權限 (“openid” 以外和「離線\_存取」)。 否則，`authorize` 要求將會結束並發生失敗。

## <a name="the-returned-token"></a>傳回的權杖

在成功產生**存取\_權杖** (從 `authorize` 或 `token` 端點)，會出現下列宣告︰

| 名稱 | 宣告 | 說明 |
| --- | --- | --- |
|對象 |`aud` |權杖授與存取權的單一資源之應用程式識別碼\*\*。 |
|Scope |`scp` |授與給資源的權限。 多個授與權限將會以空格隔開。 |
|授權的合作對象 |`azp` |起始要求之用戶端應用程式的應用程式識別碼\*\*。 |

當您的 API 收到**存取\_權杖**時，它必須[驗證權杖](active-directory-b2c-reference-tokens.md)以證明權杖的真實性，並具有正確的宣告。

我們歡迎意見反應和建議！ 如果您無法完成此主題，或者有改進此內容的建議，非常歡迎您在頁面底部提供意見反應。 對於功能要求，請將它們新增到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。

