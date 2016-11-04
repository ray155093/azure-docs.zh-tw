---
title: 如何調整 Azure Functions | Microsoft Docs
description: 了解 Azure Functions 如何調整以符合您事件驅動工作負載的需求。
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: ''
tags: ''
keywords: azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu

---
# 如何調整 Azure Functions
## 簡介
Azure Functions 的優點是只會視需要使用計算資源。這表示閒置 VM 不需要付費，或必須保留需要時的容量。相反地，平台會在您的程式碼執行時配置計算能力、視需要相應增加來處理負載，然後在程式碼未執行時相應減少。

這項新功能的機制是「動態服務方案」。

本文概述動態服務方案的運作方式，以及平台如何依需求進行調整來執行您的程式碼。

如果您還不熟悉 Azure Functions，請務必檢查 [Azure Functions 概觀](functions-overview.md)文章深入了解其功能。

## 設定 Azure Functions
兩個主要設定與調整有關︰

* [Azure App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)或動態服務方案
* 執行環境的記憶體大小

函數的成本會根據所選取的服務方案而變更。使用動態服務方案時，成本是執行時間、記憶體大小與執行次數的函數。只有在您的程式碼實際執行時才會產生費用。

App Service 方案在現有 VM 上裝載函數，而這些 VM 也可能用來執行其他程式碼。每個月支付這些 VM 之後，在其上執行函數即不需額外付費。

## 選擇服務方案
建立函數時，您可以選擇根據動態服務方案或 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)來加以執行。在 App Service 方案中，您的函數將在專用 VM 上執行，就像現在的 Web 應用程式運作方式一樣，針對基本、標準或進階 SKU。系統會將此專用 VM 配置給您的應用程式和函數，而且不論程式碼是否正在主動執行，都可以使用。如果您的現有、使用量過低的 VM 已經執行其他程式碼，或者預期會持續執行函數或近乎持續執行函數，這會是不錯的選擇。VM 會減少執行階段和記憶體大小的成本。如此一來，您可以限制許多長時間執行函數的成本，成為執行所在的一或多個 VM 的成本。

[!INCLUDE [動態服務方案](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0803_2016-->