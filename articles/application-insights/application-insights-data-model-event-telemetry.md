---
title: "Azure Application Insights 遙測資料模型 - 事件遙測 | Microsoft Docs"
description: "事件遙測的 Application Insights 資料模型"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>事件遙測：Application Insights 資料模型

事件是代表應用程式中發生動作的時間點。 通常它是使用者互動，例如按一下按鈕或簽出訂單。 它也可以是初始化或組態更新等應用程式生命週期事件。 事件名稱應該為簡短低基數的字串。 

事件在語意上可能會或可能不會與要求相互關聯。 不過，如果使用得當，事件遙測比要求或追蹤更重要。 事件代表商務遙測，且應該是個別、較不主動取樣。

## <a name="name"></a>名稱

事件名稱。 讓它保持較低的基數，以便正確群組並提供實用的計量。

最大長度︰512 個字元

## <a name="custom-properties"></a>自訂屬性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自訂度量

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>後續步驟

- 如需 Application Insights 類型和資料模型，請參閱[資料模型](/application-insights-data-model.md)。
- 了解如何使用[自訂事件和計量的 Application Insights API](/app-insights-asp-net-dependencies.md)。
- 查看 Application Insights 支援的[平台](/app-insights-platforms.md)。

