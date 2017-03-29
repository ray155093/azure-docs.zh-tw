---
title: "選擇 Azure Functions 主控方案 | Microsoft Docs"
description: "了解 Azure Functions 如何調整以符合您事件驅動工作負載的需求。"
services: functions
documentationcenter: na
author: dariagrigoriu
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
ms.date: 03/14/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 4eb138348686e9d7befe4d5433d174374977c2a1
ms.lasthandoff: 03/22/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>選擇正確的 Azure Functions 服務方案

## <a name="introduction"></a>簡介

Azure Functions 有兩個不同的服務方案︰取用方案和 App Service 方案。 取用方案會在您的程式碼執行時自動配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應縮小。 這表示您不必支付閒置 VM 的費用，或必須預先保留容量。 本文著重於取用服務方案。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

如果您還不熟悉 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)文章。

## <a name="service-plan-options"></a>服務方案選項

當您建立函式應用程式時，您必須針對應用程式中包含的函式設定主控方案。 可用的主控方案︰**取用方案**和 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 目前必須在函式應用程式的建立期間進行這項選擇。 建立之後，您就無法在這兩個選項之間變更。 您可以在 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的各層之間調整。 因為調整是動態的，所以目前不支援取用方案的變更。

### <a name="consumption-plan"></a>取用方案

在**取用方案**中，函式應用程式會指派給計算處理執行個體。 如有需要，會動態新增或移除更多執行個體。 此外，您的函式會以平行方式執行，將處理要求所需的總時間降至最低。 每個函式的執行時間會依包含函式應用程式彙總。 成本是由記憶體大小和函數應用程式中所有函式的總執行時間來決定。 當您有間歇性運算需求，或者作業執行時間很短，請使用使用情況方案。 這個方案可讓您僅針對使用的運算資源付費。 下一節會詳述取用方案的運作方式。

### <a name="app-service-plan"></a>App Service 方案

在 **App Service 方案**中，您的函式應用程式會在專用 VM 上執行，就像現在的 Web Apps 針對基本、標準或進階 SKU 運作一樣。 系統會將專用 VM 配置給您的 App Service 應用程式和函式應用程式，而且不論程式碼是否正在主動執行，都可以使用。 如果您的現有、使用量過低的 VM 已經執行其他程式碼，或者預期會持續執行函數或近乎持續執行函數，這會是不錯的選擇。 VM 會減少執行階段和記憶體大小的成本。 如此一來，您就可以將許多長時間執行函式的成本，侷限在函式執行所在 VM 所需的成本。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

## <a name="how-the-consumption-plan-works"></a>取用方案的運作方式

使用情況方案會根據函數應用程式中執行函式的需求來新增額外的處理執行個體，藉此自動調整 CPU 和記憶體資源。 每個函式應用程式處理執行個體最多可配置 1.5 GB 的記憶體資源。

在執行取用方案時，如果函式應用程式已進入閒置狀態，則處理新 Blob 時最多會有 10 分鐘的延遲。 如果函式應用程式正在執行，Blob 的處理速度會較快。 為了避免發生此初始延遲，您可以使用一般的 App Service 方案並啟用 [永遠開啟]，或使用其他機制來觸發 Blob 的處理，例如包含 Blob 名稱的佇列訊息。 

建立「函數應用程式」時，您必須建立或連結支援 Blob、「佇列」及「表格」儲存體的一般用途「Azure 儲存體」帳戶。 Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函數執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶 (包括進階儲存體) 和搭配 ZRS 複寫的一般用途儲存體帳戶。 建立「函數應用程式」時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

使用「使用情況主控方案」時，函數應用程式內容 (例如函式程式碼檔案和繫結組態) 會儲存在主要儲存體帳戶的「Azure 檔案」共用上。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介] (../storage/storage-introduction.md#introducing-the-azure-storage-services)。

### <a name="runtime-scaling"></a>執行階段調整

函式會使用調整控制器，根據設定的觸發程序來評估計算需求，以及決定何時相應放大或縮小。 調整控制器會持續處理記憶體需求和觸發程序特定資料點的提示。 例如，當使用「Azure 佇列儲存體」觸發程序時，資料點包含舊項目的佇列長度和佇列時間。

![](./media/functions-scale/central-listener.png)

調整單位是函式應用程式。 本例中，相應放大表示新增更多的函式應用程式執行個體。 反之，當計算需求降低時，便會移除函式應用程式執行個體。 執行個體數目最終會在沒有任何函式執行時相應減少為零個。 

### <a name="billing-model"></a>計費模式

[Azure Functions 價格頁面](https://azure.microsoft.com/pricing/details/functions)會詳細說明取用方案的計費方式。 使用量只會在程式碼執行的時間，依每個函式應用程式來報告。 計費單位如下︰ 
* **以 GB-s (GB-秒數) 為單位的資源取用量**會計算為在函式應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* 每次**執行**函式以回應繫結所觸發的事件，就會計入一次執行。

