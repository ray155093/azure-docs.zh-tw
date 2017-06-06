---
title: "Azure 轉送 API 概觀 | Microsoft Docs"
description: "可用的 Azure 轉送 API 概觀"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: edc730aa383b07dc308da8a160203faf3636208b
ms.contentlocale: zh-tw
ms.lasthandoff: 03/27/2017

---

# <a name="available-relay-apis"></a>可用的轉送 API

## <a name="runtime-apis"></a>執行階段 API

以下是所有目前可用的轉送執行階段用戶端清單。

如需每個執行階段程式庫之狀態的詳細資料，請參閱[其他資訊](#additional-information)。

| 語言/平台 | 可用的功能 | 用戶端封裝 | 存放庫 |
| --- | --- | --- | --- |
| .NET Standard | 混合式連線 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF 轉送 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| 節點 | 混合式連線 | [hyco-ws](https://www.npmjs.com/package/hyco-ws)<br/>[hyco-websocket](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>其他資訊

#### <a name="net"></a>.NET
.NET 生態系統有多個執行階段，因此有多個適用於事件中樞的 .NET 程式庫。 .NET Standard 程式庫可使用 .NET Core 或 .NET Framework 執行，然而 .NET Framework 程式庫只能在 .NET Framework 環境中執行。 如需 .NET Framework 的詳細資訊，請參閱[架構版本](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 轉送，請造訪下列連結：
* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [轉送常見問題集](relay-faq.md)
