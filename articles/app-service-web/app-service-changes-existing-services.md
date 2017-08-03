---
title: "Azure App Service 和它對現有 Azure 服務的影響"
description: "說明新的 Azure App Service 和其功能如何影響 Azure 中的現有服務。"
services: app-service
documentationcenter: 
author: yochay
manager: nirma
editor: 
ms.assetid: 86c6a292-3c33-49f4-890c-89cc0321b397
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: yochaykk
ms.translationtype: Human Translation
ms.sourcegitcommit: 75d42f41e6218d83cda00f2ef8926d6ca1f0aacd
ms.openlocfilehash: 8f458884d0ea649dffa5f92fd459bbaaa9f5e4cf
ms.contentlocale: zh-tw
ms.lasthandoff: 12/07/2016


---
# <a name="azure-app-service-and-existing-azure-services"></a>Azure App Service 和現有的 Azure 服務
本文概述現有 Azure 服務的變更做為將數個 Azure 服務一起帶入 [Azure App Service](https://azure.microsoft.com/services/app-service/)(新的整合服務) 的變更一部分。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>概觀
[Azure App Service](https://azure.microsoft.com/services/app-service/) 是一種新的且唯一的雲端服務，可讓開發人員建立適用於任何平台和任何裝置的 Web 和行動應用程式。 App Service 是一種整合解決方案，設計目的是簡化重複的程式碼撰寫函式、整合企業和 SaaS 系統，並自動化商務程序，同時滿足您的安全性、可靠性和延展性需求。

App Service 將下列現有 Azure 服務 - [網站](https://azure.microsoft.com/services/websites/)、[行動服務](https://azure.microsoft.com/services/mobile-services/)和 [Biztalk 服務](https://azure.microsoft.com/services/biztalk-services/)整合為單一結合服務，還增加強大的新功能。  App Service 可讓您裝載下列應用程式類型：

* Web Apps
* Mobile Apps
* API Apps
* 邏輯應用程式

下表說明現有 Azure 服務如何對應至應用程式服務和其內可用的應用程式類型。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">現有 Azure 服務</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">變更內容</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 網站</td>
<td align="left">Web Apps</td>
<td align="left"><li>針對 Azure 網站，已嚴格限制 App Service 將名稱「網站」變更為 Web Apps。
<p><li>所有您的現有網站執行個體現在都是 App Service 中的 Web Apps。</p>
<p><li>您可以透過 <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure 入口網站</a>存取現有網站，在此入口網站中，您將在 [Web Apps]<em></em> 下找到您的所有現有網站。</p>
<p><li><em>Web 主控方案</em>現在為 <em>App Service 方案</em>。 <em>App Service 方案</em>可以裝載 App Service 類型的任何應用程式，例如 Web、行動、邏輯或 API 應用程式。</p>
<p><li>Azure App Service Web Apps 已正式推出。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">深入了解 Web Apps</a>。</p></td>
</tr>
<tr class="even">
<td align="left">Azure 行動服務</td>
<td align="left">行動應用程式</td>
<td align="left"><p><li>行動服務繼續可當作獨立服務，並仍提供完整支援。</p>
<p><li>Mobile Apps 是 App Service 中的應用程式類型，整合了行動服務和其他項目的所有功能。</p>
<p><li>可以輕鬆地<a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">從行動服務移轉到 Mobile Apps</a>。</p>
<p><li>由於隸屬於 App Service，Mobile Apps 可以取得行動服務以外的新功能，例如與內部部署和 SaaS 系統整合、預備位置、更好的縮放選項，以及其他。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">深入了解 Mobile Apps</a>。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API 應用程式</td>
<td align="left">
<p><li>API 應用程式是 App Service 中的新應用程式類型，可讓您在雲端中輕鬆地建置和使用 API。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">深入了解 API Apps</a>。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logic Apps</td>
<td align="left">
<p><li>Logic Apps 是 App Service 中的新應用程式類型，可讓您輕鬆地自動化商務程序。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">深入了解 Logic Apps</a>。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 服務</td>
<td align="left">BizTalk API 應用程式</td>
<td align="left">
<li><p>BizTalk 服務繼續可當作獨立服務，並仍提供完整支援。</p>
<li><p>BizTalk 服務的所有功能都整合到 App Service 中成為 API Apps，可讓使用者執行企業應用程式整合和 B2B 整合案例，與 App Service 中任何應用程式類型整合在一起。</p>
<li><p>現在，透過 Logic Apps，您可以使用視覺化設計體驗，將商務程序自動化來建立工作流程。</p></td>
</tr>
</tbody>
</table>

若要深入了解，請瀏覽 [App Service 文件](https://azure.microsoft.com/documentation/services/app-service/)。


