---
title: "在具有 Azure Mobile Apps 的 iOS 上新增驗證"
description: "了解如何使用 Azure Mobile Apps 透過眾多身分識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>將驗證新增至您的 iOS 應用程式
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入 [iOS 快速入門] 專案。 本教學課程以 [iOS 快速入門] 教學課程為基礎，您必須先完成該教學課程。

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>限制只有通過驗證的使用者具有權限
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，按下 [執行] 以啟動應用程式。 因為應用程式嘗試以未驗證的使用者身分來存取後端，但 TodoItem 資料表現在需要驗證，所以會引發例外狀況。

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>將驗證加入應用程式
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[iOS 快速入門]: app-service-mobile-ios-get-started.md

[Azure 入口網站]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


