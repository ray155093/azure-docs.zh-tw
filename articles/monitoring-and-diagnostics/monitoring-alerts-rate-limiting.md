---
title: "SMS、電子郵件和 Webhook 的速率限制 | Microsoft Docs"
description: "活動記錄中發生特定事件時，透過 SMS、Webhook 及電子郵件收到通知。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: b2e954405500921c0c1e9c7cd71ce57130c98d64
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>SMS、電子郵件和 Webhook 的速率限制
速率限制是當傳送了太多通知給特定電話號碼或電子郵件時所發生的通知暫停。 速率限制可確保警示可管理且可採取動作

SMS 和電子郵件的規則相同。 以下項目的速率限制閾值
 - SMS - 1 小時內 10 個訊息
 - 電子郵件 - 1 小時 100 個訊息

## <a name="rate-limit-rules"></a>速率限制規則
- 當特定電話號碼或電子郵件收到的通知超過閾值時，會受到速率限制
- 電話號碼或電子郵件可以是跨許多訂用帳戶動作群組的一部分。 速率限制適用於所有訂用帳戶，也就是說，即使是傳送自多個訂用帳戶，只要達到閾值即會套用。  
- 當電話號碼或電子郵件受到速率限制時，將會傳送另一個通知來溝通速率限制。 通知會說明速率限制到期的時間。

## <a name="rate-limit-of-webhooks"></a>Webhook 的速率限制 ##
目前沒有對 Webhook 的既有速率限制。

## <a name="next-steps"></a>後續步驟 ##
進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)  
取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示  
如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)

