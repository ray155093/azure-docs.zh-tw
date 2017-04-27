---
title: "Azure RemoteApp - how do network bandwidth and quality of experience work together? (Azure RemoteApp - 如何兼顧網路頻寬和體驗品質？) | Microsoft Docs"
description: "了解 Azure RemoteApp 中的網路頻寬如何影響您使用者的體驗品質。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 74ebc1fb-5187-4056-b08c-0e03b5ecaca6
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1a1f772c12b2eb4470bd239f0325cc4ae18b9680
ms.lasthandoff: 03/31/2017


---
# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - how do network bandwidth and quality of experience work together? (Azure RemoteApp - 如何兼顧網路頻寬和體驗品質？)
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

當您查看 Azure RemoteApp 所需的 [整體網路頻寬](remoteapp-bandwidth.md) 時，請記住下列因素；這些是影響整體使用者體驗之動態系統的所有部分。 

* **可用的網路頻寬和目前網路狀況** - 相同網路上特定時間的一組參數 (遺失、延遲、抖動) 可能會影響應用程式串流體驗，這表示會降低整體使用者體驗。 網路中的可用頻寬是壅塞、隨機遺失、延遲的功能，因為所有這些參數都會影響壅塞控制機制，進而控制傳輸速度來避免衝突。  例如，遺失的網路或具有高延遲的網路會造成較差的使用者體驗，甚至在具有 1000 MB 頻寬的網路上也是一樣。 遺失和延遲會根據相同網路上的使用者數目以及這些使用者的動作 (例如，觀賞影片、下載或上傳大型檔案、列印) 而不同。
* **使用案例** - 體驗取決於使用者在相同網路上個人執行的動作以及以群組形式執行的動作。 例如，讀取一張投影片僅需要更新單一畫面格；如果使用者瀏覽並捲動文字文件的內容，則每秒需要更新較多的畫面格。 在此案例中，與伺服器之間的來回通訊最後會消耗更多的網路頻寬。 也請考量極端範例︰多位使用者觀賞高畫質影片 (例如 4K 解析度)、進行 HD 電話會議、玩 3D 視訊遊戲，或在 CAD 系統上工作。 所有這些動作甚至可能會讓真正高頻寬的網路無法使用。
* **螢幕解析度和螢幕數目** - 完整更新較大的螢幕比更新較小的螢幕，需要較多的網路頻寬。 基礎技術善於編碼和僅傳輸已更新的螢幕區域，但偶而需要更新整個畫面螢幕。 使用者的螢幕解析度較高 (例如 4K 解析度) 時，該更新所需的網路頻寬會高於解析度低的螢幕 (如 1024x768px)。 如果您使用數個螢幕進行重新導向，則適用這個相同的邏輯。 頻寬需要隨著螢幕數目而增加。
* **剪貼簿和裝置重新導向** - 這不是很明顯的問題，但在許多情況下，如果使用者將大量資料儲存至剪貼簿，則需要一些時間，將該資訊從遠端桌面用戶端傳送到伺服器。 在上游傳送剪貼簿內容的體驗可能會影響下游體驗。 這也適用於裝置重新導向 - 如果掃描器或網路攝影機產生很多需要往上游傳送到伺服器的資料、印表機需要接收大型文件，或本機存放區必須可供在雲端執行的應用程式複製大型檔案，則使用者可能會注意到畫面格減少或暫時「凍結」，因為裝置重新導向所需的資料會增加網路頻寬需求。 

當您評估網路頻寬需求時，請務必考慮將所有這些因素當成一個系統來運作。

現在，請回到[主要網路頻寬](remoteapp-bandwidth.md)一文，或繼續測試您的[網路頻寬](remoteapp-bandwidthtests.md)。

## <a name="learn-more"></a>詳細資訊
* [Estimate Azure RemoteApp network bandwidth usage (評估 Azure RemoteApp 網路頻寬使用量)](remoteapp-bandwidth.md)
* [Azure RemoteApp - testing your network bandwidth usage with some common scenarios (Azure RemoteApp - 利用一些常見案例測試您的網路頻寬使用量)](remoteapp-bandwidthtests.md)
* [Azure RemoteApp network bandwidth - general guidelines (if you can't test your own) (Azure RemoteApp 網路頻寬 - 一般指導方針 (如果您無法自行測試))](remoteapp-bandwidthguidelines.md)


