---
title: "Microsoft Azure 上的應用程式架構 | Microsoft Docs"
description: "涵蓋常見設計模式的架構概觀。"
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Microsoft Azure 上的應用程式架構
本文列出可用於建置使用 Microsoft Azure 之應用程式的資源。 其中會包含以視覺化方式繪製並描述軟體系統的工具。

## <a name="design-patterns-poster"></a>設計模式海報
Microsoft patterns & practices 已出版[雲端設計模式](http://msdn.microsoft.com/library/dn568099.aspx)一書，可在 MSDN 上瀏覽以及下載 PDF。 另外，也有以視覺化方式列出所有模式的大型海報。

![patterns & practices 雲端模式海報](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>繪製符號和圖示集
[檢視 Visio 和符號訓練影片](http://aka.ms/CnESymbolsVideo)，然後[下載雲端和企業符號集](http://aka.ms/CnESymbols)以協助建立可說明 Azure、Windows Server、SQL Server 和其他產品的技術資料。 您可以在下列免費或收費項目使用這些符號。  
- 架構圖 
- 訓練教材 
- 簡報 
- 資料表 
- 資訊圖 
- 技術白皮書 
- 協力廠商書籍 (如果該書籍可訓練人員使用 Microsoft 產品)。

這些符號並非設計來在使用者介面中使用，但您可以要求權限。  Azure 服務可以使用。 也就是說，如果您以和 Azure 入口網站相同的方式使用符號來代表相同物件的話。  例如，Azure 服務匯流排的符號應該只用來代表和存取 Azure 服務匯流排物件。 協力廠商符號非 Microsoft 所擁有。 請到這些公司的網站來了解其圖示的使用規則。

CnE 符號使用 Visio、SVG 和 PNG 格式。 集合中包含如何在 PowerPoint 中輕鬆使用符號的其他指示。 符號集每季出貨，並隨著新服務的發行進行更新。

[Microsoft Office Visio 樣板](http://www.microsoft.com/en-us/download/details.aspx?id=35772)提供 Microsoft Office 和相關技術的其他符號，雖然它們不是最適用於架構圖表，但是 CnE 集是。   

如有特定問題或要反應意見，請寄送電子郵件給我們：[CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)。 我們想要知道您的想法，包括正面意見回應，讓我們知道可以繼續投入時間。

![雲端和企業符號/圖示集](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Microsoft 架構憑證課程
Microsoft 已在 2015 年 8 月建立支援 Microsoft 認證測驗 70-534 的架構課程。 它 [可在 EDX.ORG 上免費提供](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)。  它使用 [3D 藍圖 Visio 範本](#3d-blueprint-visio-template)。

![Microsoft 架構憑證課程](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Microsoft 解決方案
Microsoft 發佈一組高階[解決方案架構](http://aka.ms/azblueprints)，示範如何使用 Microsoft 產品來建置特定類型的系統。

在之前，Microsoft 發佈了一組說明範例架構的等距 3D 藍圖。 這些藍圖已由解決方案架構取代。 藍圖的連結已重新導向，以指向解決方案。 如果您基於某些原因需要存取以前的藍圖資料，請將電子郵件寄到 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)，並附上您的要求。   

藍圖和解決方案架構圖皆使用部分的[雲端和企業符號集](http://aka.ms/CnESymbols)。   

![Microsoft 架構藍圖 3D 圖表](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>3D 藍圖 Visio 範本
現已不再使用的 [Microsoft 架構藍圖](http://aka.ms/azblueprints) 的 3D 版本一開始是以非 Microsoft 工具來建立。 Visio 2013 (和更新版本) 範本於 2015 年 8 月 5 日推出，做為[分佈在 EDX.ORG 上之 Microsoft 架構憑證課程](#microsoft-architecture-certification-course)的一部分。

此範本在課程外也可供使用。

* [檢視訓練影片](http://aka.ms/3dBlueprintTemplateVideo) ，讓您知道它的功能   
* 下載 [Microsoft 3D 藍圖 Visio 範本](http://aka.ms/3DBlueprintTemplate)
* 下載[雲端和企業符號集](https://www.microsoft.com/en-us/download/details.aspx?id=41937)以搭配 3D 範本使用。 

如果有訓練教材無法解答的特定問題或要提供意見反應，請將電子郵件寄至以下的地址 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 。 該範本已不再進行開發。 但它仍然有用且相關，因為它可以使用任何 PNG 或已更新的[雲端和企業符號集](http://aka.ms/CnESymbols)。  

![Microsoft 3D 藍圖 Visio 範本](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>架構資訊圖
Microsoft 出版了數個與架構相關的海報/資訊圖。 其中包括[建置真實世界的雲端應用程式](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/)和[使用雲端服務調整](https://azure.microsoft.com/documentation/infographics/cloud-services/)。

![Azure 架構資訊圖](./media/architecture-overview/AzureArchInfographicThumb.jpg)

