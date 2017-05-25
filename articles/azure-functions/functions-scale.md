---
title: "Azure Functions 取用和 App Service 方案 | Microsoft Docs"
description: "了解 Azure Functions 如何調整以符合您事件驅動工作負載的需求。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: donnam, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25df99203ccb194d75985fca42d3d05609dc7d7c
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Azure Functions 取用和 App Service 方案 

## <a name="introduction"></a>簡介

Azure Functions 可以兩個不同的模式執行︰取用方案和 App Service 方案。 取用方案會在您的程式碼執行時自動配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應減少。 因此，您不必支付閒置 VM 的費用，或必須預先保留容量。 本文的重點是取用方案。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

如果您還不熟悉 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)文章。

當您建立函式應用程式時，您必須針對應用程式中包含的函式設定主控方案。 可用的主控方案有**取用方案**和 [**App Service 方案**](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 不管用哪一種，都是以 Azure Functions 主機的執行個體執行函式。 方案類型控制了︰1) 主機執行個體如何相應放大，2) 每一部主機可用的的資源。

目前必須在函數應用程式的建立期間選擇方案類型，且之後不能變更。 您可以在 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的各層之間調整。 在取用方案中，Azure Functions 會自動處理所有的資源配置。

## <a name="consumption-plan"></a>取用方案

當使用**取用方案計劃**時，會根據傳入事件的數目，動態新增和移除 Azure Functions 主機。 這個方案會自動調整，您只需支付您的函式執行時使用的計算資源。 在取用方案中，函式最多可執行五分鐘。 

計費是根據執行時間和使用的記憶體，會針對函數應用程式內的所有函式加以彙總。 如需詳細資訊，請參閱 [Azure Functions 價格頁面]。

取用方案是預設值，具有下列優點︰
- 只有當您的函式執行時才需付費。
- 會自動調整規模，即使在高負載期間也會

## <a name="app-service-plan"></a>App Service 方案

在 **App Service 方案**中，您的函數應用程式是依據基本、標準或進階 SKU 在專用的 VM 上執行，就像 Web Apps 一樣。 會配置專用的 VM 給您的 App Service 應用程式，這表示函式主機執行不間斷。

在下列情況中請考慮使用 App Service 方案︰
- 您有現有的、使用量過低的 VM 已在執行其他 App Service 執行個體。
- 您期望您的函數應用程式連續執行或接近連續執行。
- 您需要的 CPU 或記憶體選項比取用方案所提供的更多。
- 您的執行時間超過取用方案允許的執行時間上限。

VM 會減少執行階段和記憶體大小的成本。 如此一來，您不會支付超過您配置的 VM 執行個體的成本。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

使用 App Service 方案時，您可以透過手動新增更多 VM 執行個體來相應放大，或者您可以啟用自動規模調整。 如需詳細資訊，請參閱[手動或自動調整執行個體計數規模](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。 您也可以透過選擇不同的 App Service 方案來相應增加。 如需詳細資訊，請參閱[在 Azure 中為應用程式進行相應增加](../app-service-web/web-sites-scale.md)。 若計畫在 App Service 方案上執行 JavaScript 函式，您應該選擇核心數目較少的方案。 如需詳細資訊，請參閱 [JavaScript 函式參考資料](functions-reference-node.md#choose-single-core-app-service-plans)。  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
###永遠開啟

如果您執行 App Service 方案，應該啟用 [永遠開啟] 設定，好讓您的函數應用程式能夠正確執行。 在 App Service 方案中，函式的執行階段會在無作用幾分鐘後進入閒置狀態，因此只有 HTTP 觸發程序真的會「喚醒」您的函式。 這類似於 WebJobs 必須啟用「永遠開啟」的方式。 

只有 App Service 方案有永遠開啟選項。 在取用方案中，平台會自動啟動 函數應用程式。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

不論是取用方案或 App Service 方案，函數應用程式都需要有支援 Blob、佇列、表格儲存體的 Azure 儲存體帳戶。 Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函數執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶 (包括進階儲存體) 和搭配 ZRS 複寫的一般用途儲存體帳戶。 建立「函數應用程式」時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介] (../storage/storage-introduction.md#introducing-the-azure-storage-services)。

## <a name="how-the-consumption-plan-works"></a>取用方案的運作方式

取用方案會根據事件函式被觸發的事件數目，新增額外的 Functions 主機執行個體，藉此自動調整 CPU 和記憶體資源。 Functions 主機的每個執行個體僅限於最多 1.5 GB 記憶體。

使用「取用」主控方案時，函式的程式碼檔案會儲存在主要儲存體帳戶的「Azure 檔案」共用上。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

> [!NOTE]
> 在執行取用方案中使用 blob 觸發程序時，如果函數應用程式已進入閒置狀態，則處理新 blob 時最多會有 10 分鐘的延遲。 一旦函數應用程式開始執行，則會立即處理 blob。 為了避免發生此初始延遲，請考慮下列做法︰
> - 使用 App Service 方案並啟用「永遠開啟」。
> - 使用其他機制來觸發 blob 的處理，像是包含 blob 名稱的佇列訊息。 如需範例，請參閱[具有 blob 輸入繫結的佇列觸發程序](functions-bindings-storage-blob.md#input-sample)。

### <a name="runtime-scaling"></a>執行階段調整

Azure Functions 使用名為「縮放控制器」的元件來監視事件的速率，並判斷是否相應放大或相應減少。 縮放控制器會在每種觸發程序類型使用啟發學習法。 例如，當使用「Azure 佇列儲存體」觸發程序時，會根據佇列長度和最舊佇列訊息的壽命調整規模。

調整的單位是函數應用程式。 相應放大時，會配置更多資源來執行 Azure Functions 主機的多個執行個體。 反之，當計算需求降低時，便會移除 Functions 主機的執行個體。 執行個體的數目最終會在函數應用程式中沒有任何函式執行時相應減少至零個。

![](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>計費模式

[Azure Functions 價格頁面]會詳細說明取用方案的計費方式。 使用量是在函數應用程式層級彙總，且只會計算函式程式碼執行的時間。 計費單位如下︰ 
* **以 GB-s (十億位元組-秒) 為單位的資源取用量**。 會計算為在函數應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* **執行**。 每次函式為回應事件而執行 (由繫結所觸發)，就算一次。

[Azure Functions 價格頁面]: https://azure.microsoft.com/pricing/details/functions
