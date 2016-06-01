<properties
   pageTitle="如何調整 Azure Functions | Microsoft Azure"
   description="了解 Azure Functions 如何調整以符合您事件驅動工作負載的需求。"
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="edlaure"/>
  
# 如何調整 Azure Functions
     
## 簡介

Azure Functions 的其中一個優點是您的執行中程式碼只會視需要使用資源。這表示閒置 VM 不需要付費，或必須保留需要時的容量。相反地，平台會在您的程式碼執行時配置計算能力、視需要相應增加來處理負載，然後在程式碼未執行時再次相應減少。

這項新功能的機制是「動態服務方案」。這個新的服務方案會針對您的程式碼提供可依需求相應增加的動態容器，而您只需要支付您程式碼所使用的記憶體容量，以及執行所需的時間 (測量單位為 GB/秒)。

本文概述動態服務方案的運作方式，以及平台如何依需求進行調整來執行您的程式碼。

如果您還不熟悉 Azure Functions，請務必檢查 [Azure Functions 概觀](functions-overview.md)深入了解其功能。

## 設定函數應用程式

有兩個主要設定與調整有關︰

* [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)或動態服務方案 
* 執行環境的記憶體大小 

函數的成本會根據所選取的服務方案類型而變更。使用動態服務方案時，成本是執行時間、記憶體大小與執行次數的函數。僅使用在實際執行程式碼時產生的費用。

一般服務方案可讓您在現有 VM 上裝載函數，而這些 VM 也可能用來執行其他程式碼。每個月支付這些 VM 之後，在其上執行函數即不需額外付費。

## 選擇服務方案

建立函數應用程式時，您可以選擇根據動態服務方案 (新功能！) 或一般 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)來加以執行。在 App Service 方案中，您的函數將在專用 VM 上執行，就像現在的 Web 應用程式運作方式一樣 (針對基本、標準或進階 SKU)。系統會將此專用 VM 配置給您的應用程式和 (或) 函數，而且不論正在主動執行的程式碼為何都可以使用。如果您的現有 VM 已經執行其他程式碼，但尚未充分利用，或者預期會持續執行函數或近乎持續執行函數，這會是不錯的選擇。使用 VM 可減少執行時間和記憶體大小的成本，讓您將大量長時間執行函數的成本限制為它們在其上執行的一或多個 VM 的成本。

[AZURE.INCLUDE [動態服務方案](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0518_2016-->