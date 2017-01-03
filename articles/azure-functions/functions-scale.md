---
title: "如何調整 Azure Functions | Microsoft Docs"
description: "了解 Azure Functions 如何調整以符合您事件驅動工作負載的需求。"
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>調整 Azure Functions

## <a name="introduction"></a>簡介

Azure Functions 平台會在您的程式碼執行時配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應縮小。 這表示您不必支付閒置 VM 的費用，或必須預先保留容量。 這項功能的機制是取用服務方案。 本文提供取用服務方案運作方式的概觀。 

如果您還不熟悉 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)文章。

## <a name="choose-a-service-plan"></a>選擇服務方案

當您建立函式應用程式時，您必須針對應用程式中包含的函式設定主控方案。 可用的主控方案︰**取用方案**和 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 目前必須在函式應用程式的建立期間進行這項選擇。 建立之後，您就無法在這兩個選項之間變更。 您可以在 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的各層之間調整。 因為調整是動態的，所以目前不支援取用方案的變更。

### <a name="consumption-plan"></a>取用方案

在**取用方案**中，函式應用程式會指派給計算處理執行個體。 如有需要，會動態新增或移除更多執行個體。 此外，您的函式會以平行方式執行，將處理要求所需的總時間降至最低。 每個函式的執行時間會依包含函式應用程式彙總。 成本會隨記憶體大小及函式應用程式中所有函式的總執行時間 (以 GB 秒計算) 而變化。 如果您的計算需求是間歇性的，或作業時間通常很短，這會是您的最佳選擇，因為它可讓您只支付實際使用的計算資源。 

### <a name="app-service-plan"></a>App Service 方案

在 **App Service 方案**中，您的函式應用程式會在專用 VM 上執行，就像現在的 Web Apps 針對基本、標準或進階 SKU 運作一樣。 系統會將專用 VM 配置給您的 App Service 應用程式和函式應用程式，而且不論程式碼是否正在主動執行，都可以使用。 如果您的現有、使用量過低的 VM 已經執行其他程式碼，或者預期會持續執行函數或近乎持續執行函數，這會是不錯的選擇。 VM 會減少執行階段和記憶體大小的成本。 如此一來，您可以限制許多長時間執行函數的成本，成為執行所在的一或多個 VM 的成本。

## <a name="consumption-service-plan"></a>取用服務方案

取用服務方案會根據函式應用程式中函式的執行階段需求來新增額外的處理執行個體，藉此自動調整 CPU 和記憶體資源。 每個函式應用程式處理執行個體最多可配置 1.5GB 的記憶體資源。

### <a name="runtime-scaling"></a>執行階段調整

Azure Functions 平台會使用中央接聽程式，根據設定的觸發程序來評估計算需求，以及決定何時相應放大或縮小。 中央接聽程式會持續處理記憶體需求和觸發程序特定資料點的提示。 例如，在 Azure 佇列儲存體觸發程序的案例中，資料點包含舊項目的佇列長度和佇列時間。

![](./media/functions-scale/central-listener.png)

調整單位是函式應用程式。 本例中，相應放大表示新增更多的函式應用程式執行個體。 反之，當計算需求下降時，函式應用程式執行個體會遭到移除 - 最後沒有執行個體執行時即相應縮小至零。 

### <a name="billing-model"></a>計費模式

[Azure Functions 價格頁面](https://azure.microsoft.com/pricing/details/functions)會詳細說明取用服務方案的計費方式。 使用量只會在程式碼執行的時間，依每個函式應用程式來報告。 計費單位如下︰ 
* **以 GB-s (GB-秒數) 為單位的資源取用量**會計算為在函式應用程式中執行之所有函式的記憶體大小和執行時間組合。 
* 每次**執行**函式以回應繫結所觸發的事件，就會計入一次執行。



<!--HONumber=Nov16_HO4-->


