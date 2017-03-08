---
title: "使用 Visual Studio 2015 部署至 Azure App Service | Microsoft Docs"
description: "本文說明如何使用 Visual Studio 2015 和 Azure SDK 將「Web 應用程式」、「API 應用程式」或「行動裝置應用程式」部署至 Azure Government。"
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>使用 Visual Studio 2015 部署至 Azure App Service
本文說明如何使用 Visual Studio 2015 將 Azure App Service 應用程式 (API 應用程式、Web 應用程式、行動裝置應用程式) 部署至 Azure Government。

## <a name="prerequisites"></a>必要條件
* 參閱 [Visual Studio 必要條件] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites)來安裝及設定 Visual Studio 2015 和 Azure SDK。
* 依照[這些指示] (documentation-government-manage-subscriptions.md) 來設定 Visual Studio 以連接到 Azure Government 帳戶。

## <a name="open-app-project-in-visual-studio"></a>在 Visual Studio 中開啟應用程式專案
* 在 Visual Studio 中開啟現有的應用程式方案\專案、依照[這些指示] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application)來建立專案，或依照[這些步驟] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application)來下載範例應用程式。
* 在 Visual Studio 中執行應用程式，以確定它可以在本機運作。

## <a name="deploy-to-azure-government"></a>部署至 Azure Government
* **設定 Visual Studio 以連接到 Azure Government 帳戶** (已經在＜必要條件＞一節中完成) 之後，部署至應用程式服務的指示就與適用於 Azure Public 的指示完全相同。
* 若要部署應用程式，請依照[這些步驟] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it)。

### <a name="references"></a>參考
* [使用 Visual Studio 將 ASP.NET Web 應用程式部署至 Azure App Service] (../app-service-web/web-sites-dotnet-get-started.md)
* 如需了解其他部署方式，請參閱[將您的應用程式部署至 Azure App Service] (../app-service-web/web-sites-deploy.md)
* 如需一般 App Service 文件，請參閱 [App Service - API Apps 文件] (../app-service-api/index.md)

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 [Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)。

