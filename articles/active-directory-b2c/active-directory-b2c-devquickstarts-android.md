---
title: "Azure Active Directory B2C：從 Android 應用程式呼叫 Web API | Microsoft Docs"
description: "本文將示範如何建立 Android「待辦事項清單」應用程式，以使用 OAuth 2.0 持有人權杖呼叫 Node.js Web API。 Android 應用程式與 Web API 會使用 Azure Active Directory B2C，來管理使用者身分識別並驗證使用者。"
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C：從 Android 應用程式呼叫 Web API
> [!WARNING]
> 我們已發佈一個更新過的 Android 程式碼範例，您可以在[這裡](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi)找到此範例。  此範例使用已藉由 Azure AD B2C 在基本案例中進行過相容性測試的協力廠商程式庫。  此程式庫使用內嵌 Web 檢視，而不是系統瀏覽器。  Google [已經宣布](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)自 2017 年 4 月 20 日起將不再支援以內嵌 Web 檢視進行 Google 帳戶登入，屆時希望支援 Google 帳戶的人將必須更新程式庫。  

>Microsoft 並不提供協力廠商程式庫的修正程式，也尚未審查這些程式庫。 問題和功能要求應導向到程式庫的開放原始碼專案。 如需詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>




<!--HONumber=Feb17_HO1-->


