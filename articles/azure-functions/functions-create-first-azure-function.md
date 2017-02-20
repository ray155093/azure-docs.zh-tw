---
title: "建立您的第一個 Azure 函式 | Microsoft Docs"
description: "在兩分鐘內建立您的第一個 Azure 函式 (無伺服器的應用程式)。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>建立您的第一個 Azure 函式
## <a name="overview"></a>Overview
Azure Functions 是事件取向的隨選計算體驗並擴充了現有的 Azure 應用程式平台，使其可實作其他 Azure 服務、SaaS 產品和內部部署系統內事件所觸發的程式碼。 透過 Azure Functions，您的應用程式會根據需求調整，而您只需要支付所用資源的費用。 Azure Functions 可您建立以各種程式設計語言實作的已排程或已觸發程式碼單位。 若要深入了解 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)。

本主題說明如何在入口網站中使用 Azure Functions 快速入門，建立由 HTTP 觸發程序叫用的簡單 "hello world" JavaScript 函式。 您也可以觀賞短片，了解如何在入口網站中執行這些步驟。

## <a name="watch-the-video"></a>觀賞影片
下列影片示範如何在本教學課程中執行基本步驟。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>從快速入門建立函式
函式應用程式可在 Azure 中主控函式的執行。 請遵循下列步驟來建立具備新函式的函式應用程式。 函式應用程式以會預設組態建立。 如需如何明確建立函式應用程式的範例，請參閱 [其他 Azure Functions 快速入門教學課程](functions-create-first-azure-function-azure-portal.md)。

您必須先具備有效的 Azure 帳戶，才可以建立第一個函式。 如果您還沒有 Azure 帳戶， [可以使用免費帳戶](https://azure.microsoft.com/free/)。

1. 移至 [Azure Functions 入口網站](https://functions.azure.com/signin) ，然後以您的 Azure 帳戶登入。
2. 輸入新函式應用程式的唯一 [名稱] 或接受自動產生的名稱，選取您偏好的 [區域]，然後按一下 [建立 + 開始]。 請注意，您必須輸入有效名稱，該名稱只可包含字母、數字和連字號。 不允許使用底線 (**_**) 字元。
3. 在 [快速入門] 索引標籤中，按一下 [WebHook + API] 及 [JavaScript]，然後按一下 [建立函式]。 隨即建立預先定義的新 JavaScript 函式。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (選擇性) 此時，您可以在快速入門中，選擇在入口網站中進行 Azure Functions 功能的快速導覽。 完成或跳過本教學課程之後，您可以使用 HTTP 觸發程序來測試新函式。

## <a name="test-the-function"></a>測試函式
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>後續步驟
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


