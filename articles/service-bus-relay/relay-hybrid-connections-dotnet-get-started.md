---
title: "開始使用轉送混合式連線 | Microsoft Docs"
description: "如何為混合式連線撰寫 C# 主控台應用程式"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>開始使用轉送混合式連線
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>將會完成的工作
由於混合式連線需要用戶端和伺服器元件，我們將在本教學課程中建立兩個主控台應用程式。 步驟如下：

1. 使用 Azure 入口網站建立轉送命名空間。
2. 使用 Azure 入口網站建立混合式連線。
3. 撰寫伺服器主控台應用程式來接收訊息。
4. 撰寫用戶端主控台應用程式來傳送訊息。

## <a name="prerequisites"></a>必要條件
1. [Visual Studio 2013 或 Visual Studio 2015](http://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2015。
2. Azure 訂用帳戶。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 入口網站建立命名空間
如果您已經建立轉送命名空間，請跳至[使用 Azure 入口網站建立混合式連線](#2-create-a-hybrid-connection-using-the-azure-portal)一節。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.使用 Azure 入口網站建立混合式連線
如果您已經建立混合式連線，請跳至[建立伺服器應用程式](#3-create-a-server-application-listener)一節。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.建立伺服器應用程式 (接聽程式)
為了接聽並接收來自轉送的訊息，我們會使用 Visual Studio 撰寫 C# 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.建立用戶端應用程式 (傳送者)
為了將訊息傳送到轉送，我們會使用 Visual Studio 撰寫 C# 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.執行應用程式
1. 執行伺服器應用程式。
2. 執行用戶端應用程式並輸入一些文字。
3. 確定伺服器應用程式主控台有將用戶端應用程式中所輸入的文字輸出。

![執行應用程式](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

恭喜您，您已建立端對端混合式連線應用程式。

## <a name="next-steps"></a>後續步驟：
* [轉送常見問題集](relay-faq.md)
* [建立命名空間](relay-create-namespace-portal.md)
* [開始使用 Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


