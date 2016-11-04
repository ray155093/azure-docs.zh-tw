---
title: Azure AD .NET 通訊協定概觀 | Microsoft Docs
description: 如何使用 HTTP 訊息來使用 Azure AD 授權存取租用戶中的 Web 應用程式和 Web API。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo

---
<!--TODO: Introduction -->

## 向 AD 租用戶註冊應用程式
首先，您必須向 Active Directory 租用戶註冊應用程式。這會讓應用程式獲得用戶端識別碼，以及讓它可以接收權杖。

* 登入 Azure 管理入口網站。
* 在左瀏覽窗格中，按一下 [Active Directory]。
* 選取要在其中註冊應用程式的租用戶。
* 按一下 [應用程式] 索引標籤，然後按一下底端隱藏式選單中的 [新增]。
* 遵照提示進行，並建立新的應用程式。在本教學課程中，不論它是 Web 應用程式或原生應用程式都沒關係，但如果您想要 Web 應用程式或原生應用程式的特定範例，請在[這裡](../articles/active-directory/active-directory-developers-guide.md)查看快速入門。
* 若為 Web 應用程式，請提供屬於應用程式基礎 URL 的**登入 URL**以供使用者登入，例如 `http://localhost:12345`。[**應用程式識別碼 URI**] 是指應用程式的唯一識別碼。慣例會使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`
* 若為原生應用程式，請提供**重新導向 URI**，以供 Azure AD 用來傳回權杖回應。輸入應用程式特定的值，例如 `http://MyFirstAADApp`
* 完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您會在後續幾節中用到此值，所以請在應用程式的 [設定] 索引標籤中複製此值。

<!---HONumber=AcomDC_0601_2016-->