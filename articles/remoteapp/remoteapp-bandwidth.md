---
title: "Estimate Azure RemoteApp network bandwidth usage (評估 Azure RemoteApp 網路頻寬使用量) | Microsoft Docs"
description: "深入了解 Azure RemoteApp 集合和應用程式的網路頻寬需求。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 3127f4c7-f532-46c3-ba9b-649f647abec1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 045410664ec70c846abbf32a36dcc8f70ac471be


---
# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Estimate Azure RemoteApp network bandwidth usage (評估 Azure RemoteApp 網路頻寬使用量)
> [!IMPORTANT]
> Azure RemoteApp 即將中止。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

Azure RemoteApp 使用遠端桌面通訊協定 (RDP)，在 Azure 雲端中執行的應用程式與您的使用者之間進行通訊。 本文提供一些基本指導方針，可用來評估該網路使用量，也可能評估每位 Azure RemoteApp 使用者的網路頻寬使用量。

估計每位使用者的頻寬使用量十分複雜，而且需要在多工作業案例中同時執行多個應用程式，而在此案例中，根據網路頻寬需求，應用程式可能會影響彼此的效能。 遠端桌面用戶端類型 (例如 Mac 用戶端與 HTML5 用戶端) 甚至可能會導致不同的頻寬結果。 為了協助您解決這些難題，我們將使用案例分成數個複寫真實案例的常用類別。 (當然，在真實案例中，混合使用各種類別，而且會依使用者而不同)。

在繼續之前，請注意，我們假設 RDP 透過低於 120 毫秒的延遲和高於 5 MB 的頻寬，提供網路上的大多數使用案例擁有極佳體驗；這是根據 RDP 使用可用網路頻寬和預估應用程式頻寬需求來進行動態調整的能力。 本文並不僅止於這些「大部分使用案例」，在其中，案例會開始回溯，而使用者體驗會開始下降。

現在，請參閱下列文章來取得詳細資料 (包括要考量的因素、基準建議，以及未包含在我們的估計值中的項目)。

* [How do network bandwidth and quality of experience work together? (如何兼顧網路頻寬和體驗品質？)](remoteapp-bandwidthexperience.md)
* [Testing your network bandwidth usage with some common scenarios (利用一些常見案例測試您的網路頻寬使用量)](remoteapp-bandwidthtests.md)
* [Quick guidelines if you don't have the time or ability to test (沒有時間或測試能力時的快速指導方針)](remoteapp-bandwidthguidelines.md)

## <a name="what-are-we-not-including"></a>未包含的內容
當您檢閱建議的測試和我們的整體 (和公認的一般) 建議時，請注意有數個我們未考慮到的因素。 例如，上傳與下載頻寬的不對稱本質所提供的使用者體驗難題。 大部分 Wi-Fi 網路的不對稱本質會額外影響效能和使用者體驗觀感。 在互動式案例中，下游流量的優先順序低於上游，而這可能會增加遺失視訊或音訊框架數目，因此影響使用者對串流體驗的觀感。 您可以進行您自己的實驗，了解什麼最適合您的特定使用案例和網路。

雖然我們討論裝置重新導向，但是我們未考慮已連接裝置 (例如儲存體、印表機、掃描器、網路攝影機和其他 USB 裝置) 所造成之網路流量的頻寬影響。 這些裝置通常會暫時導致頻寬需求變高，並在工作完成時回復正常。 但是，如果經常這樣做，則頻寬需求可能會相當可觀。

我們也不會討論一位使用者對相同網路內的其他使用者的影響。 例如，在 100 MB/s 網路上使用 4K 視訊的一位使用者可能會大幅地影響該相同網路內嘗試執行相同工作的其他使用者。 不幸的是，越來越難判斷並行使用的影響，以提供有關系統整體效能的通用或全方位建議。 我們只能說基礎通訊協定技術會善用可用的網路頻寬，但有其限制。




<!--HONumber=Dec16_HO2-->


