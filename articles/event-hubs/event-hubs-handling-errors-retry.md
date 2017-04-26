---
title: "用於處理錯誤的 Azure 事件中樞最佳做法 | Microsoft Docs"
description: "Azure 事件中樞處理錯誤與重試"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 用於處理錯誤的事件中樞最佳做法與重試

> [!NOTE]
> 
> 此主題尚未撰寫！ 
>
> 歡迎您提供意見以協助我們設定此內容的範圍與建立方法。 您可以在此 [GitHub 議題](https://github.com/Azure/azure-event-hubs/issues/305)中追蹤狀態並提供您的意見。
> 
> 深入了解如何參與 [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md) 並做出貢獻。

此文章將涵蓋︰

- 錯誤類型有哪些？
- 發生錯誤時該如何處理？
- 應該重試哪些類型的錯誤？ 不應該重試哪些類型的錯誤？
- 自訂重試原則

