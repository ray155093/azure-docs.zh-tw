---
title: "使用 Azure 入口網站建立服務匯流排命名空間 | Microsoft Docs"
description: "若要開始使用服務匯流排，您需要命名空間。 以下會說明如何使用 Azure 入口網站建立命名空間。"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0b1f6f7ec47e47f39407cdbfd5efef2a18944ecc
ms.openlocfilehash: 89610512c264cef4bd52728240ddc0a34091edea


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>使用 Azure 入口網站建立服務匯流排命名空間
命名空間是所有訊息元件的共同容器。 多個佇列和主題可以位於單一命名空間，而且命名空間通常會做為應用程式容器。 目前有 2 個不同的方式可建立服務匯流排命名空間。

1. Azure 入口網站 (本文)
2. [Resource Manager 範本][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 入口網站中建立命名空間
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

恭喜！ 您現已建立服務匯流排訊息命名空間。

## <a name="next-steps"></a>後續步驟
查看 [GitHub 範例][github-samples]，其中的範例會展示一些更進階的 Azure 服務匯流排訊息功能。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Dec16_HO1-->


