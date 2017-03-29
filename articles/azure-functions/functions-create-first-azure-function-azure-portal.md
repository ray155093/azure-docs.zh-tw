---
title: "從 Azure 入口網站建立您的第一個函式 | Microsoft Docs"
description: "歡迎使用 Azure。 從 Azure 入口網站建立您的第一個 Azure 函式。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>在 Azure 入口網站中建立您的第一個函式

本主題說明如何建立一個由 HTTP 要求叫用的簡單 "hello world" Azure 函式。 在 Azure 入口網站中建立函式之前，您必須在 Azure App Service 中建立函數應用程式，以裝載函式的執行。

若要完成此快速入門，您必須有 Azure 帳戶。 有[免費帳戶](https://azure.microsoft.com/free/)可供使用。 您也可以[試用 Azure Functions](https://azure.microsoft.com/try/app-service/functions/)，而不需向 Azure 註冊。

## <a name="create-a-function-app"></a>建立函數應用程式

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

如需詳細資訊，請參閱[從 Azure 入口網站建立函數應用程式](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>建立函式
透過使用 Azure Functions 快速入門中的這些步驟，在新的函數應用程式中建立函式。

1. 在 [快速入門] 索引標籤中，按一下 [WebHook + API] 並選擇您的函式語言，然後按一下 [建立函式]。 隨即以您所選的語言建立新的預先定義函式。  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (選擇性) 此時，您可以在快速入門中，選擇在入口網站中進行 Azure Functions 功能的快速導覽。 完成或跳過本教學課程之後，您可以藉由傳送 HTTP 要求來測試新函式。


## <a name="test-the-function"></a>測試函式
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>後續步驟
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


