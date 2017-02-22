---
title: "Azure 診斷組態結構描述版本清單 | Microsoft Docs"
description: "用來設定 Azure 虛擬機器、VM 擴展集、Service Fabric 和雲端服務中效能計數器的集合。"
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Azure 診斷版本清單
針對隨附於 Microsoft Azure SDK 的 Azure 診斷組態結構描述版本，此頁面會建立其索引。  

> [!NOTE]
> Azure 診斷是一種元件，可針對 Azure 虛擬機器、虛擬機器擴展集，Service Fabric 以及雲端服務，收集相關效能計數器和其他統計資料。  此頁面只在您使用其中一個服務時才相關。
>

Azure 診斷要與 Azure 監視器、Application Insights 和 Log Analytics 等其他 Microsoft 診斷產品搭配使用。

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 和診斷版本推出方式圖表  

|Azure SDK 版本 | Azure 診斷版本 | 模型|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | 外掛程式|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |擴充功能|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 Azure 診斷 1.0 版最早是隨附於外掛程式模型中，這表示當您安裝 Azure SDK 時，即會取得隨附於其中的 Azure 診斷版本。  

 從 SDK 2.5 (診斷版本 1.2) 開始，Azure 診斷變成延伸模型。 運用新功能的工具只能在較新的 Azure SDK 中取得，但是，任何使用診斷的雲端服務或虛擬機器都能直接從 Azure 挑選最新的上市版本。  

 例如，任何仍在使用 SDK 2.5 的使用者都可載入診斷 1.5，而不論他們先前是否使用較新的功能。  

## <a name="azure-diagnostics-schemas-index"></a>Azure 診斷結構描述索引  
[Azure 診斷 1.0 組態結構描述](azure-diagnostics-schema-1dot0.md)  

[Azure 診斷 1.2 組態結構描述](azure-diagnostics-schema-1dot2.md)  

[Azure 診斷 1.3 到 1.5 組態結構描述](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


