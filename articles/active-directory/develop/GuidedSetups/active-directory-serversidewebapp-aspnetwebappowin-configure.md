---
title: "Azure AD v2 ASP.NET Web 伺服器快速入門 - 設定 | Microsoft Docs"
description: "使用 OpenID Connect 標準，搭配傳統網頁瀏覽器型應用程式，在 ASP.NET 方案上實作 Microsoft 登入"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: zh-tw


---

## <a name="create-an-application-express"></a>建立應用程式 (快速)
現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：
1. 透過 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure) 註冊您的應用程式
2.    輸入應用程式的名稱和您的電子郵件
3.    確定已選取 [Guided Setup (引導式設定)] 選項
4.    依照指示將重新導向 URL 新增到您的應用程式

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>將您的應用程式註冊資訊新增到方案 (進階)
現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：
1. 前往 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app) 註冊應用程式
2. 輸入應用程式的名稱和您的電子郵件 
3.    確定已取消選取 [Guided Setup (引導式設定)] 選項
4.    按一下 `Add Platforms`，然後選取 `Web`
5.    回到 Visual Studio，在「方案總管」中，選取專案並查看 [屬性] 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)
6.    將 [SSL 已啟用] 變更為 `True`：<br/><br/>![專案屬性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    複製 SSL URL 並將這個 URL 新增到註冊入口網站的重新導向 URL 清單
8.    在位於根資料夾之 `web.config` 的 `configuration\appSettings` 區段下新增下列內容：

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. 用您剛剛註冊的應用程式識別碼取代 `ClientId`
10. 用您專案的 SSL URL 取代 `redirectUri` 
<!-- End Docs -->

> 注意
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>只將來自一個組織的使用者限制為登入您的應用程式
> 根據預設值，下列帳戶可以登入您的應用程式：個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及來自已經整合 Azure Active Directory 之公司或組織的公司與學校帳戶。 如果您想要讓應用程式只接受來自某一個組織的登入，請將 `web.config` 中的 `Tenant` 參數從 `Common` 取代為該組織的租用戶名稱 (例如 `contoso.onmicrosoft.com`)。 完成之後，請將您 OWIN 啟動類別中的 *ValidateIssuer* 引數變更為 `true`。
若只要允許來自某一特定組織清單的使用者登入，請將 `ValidateIssuer` 設定為 `true` 並使用 `ValidIssuers` 參數指定組織清單。
另一個選項是實作自訂方法，使用 `IssuerValidator parameter` 驗證簽發者。 如需 `TokenValidationParameters` 的相關詳細資訊，請參閱[這篇](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx) MSDN 文章。


