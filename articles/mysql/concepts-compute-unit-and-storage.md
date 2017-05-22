---
title: "說明適用於 MySQL 的 Azure 資料庫的計算單位與儲存體單位 | Microsoft Docs"
description: "說明計算單位與儲存體單位，以及當您到達計算單位或儲存體單位上限時，會發生什麼狀況。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c10253698864b5e1964fbf15b3484752a551c3b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>說明計算單位與儲存體單位
本文說明計算單位與儲存體單位的概念，以及當您到達計算單位或儲存體單位上限時，會發生什麼狀況。

## <a name="what-are-compute-units"></a>什麼是計算單位？
計算單位是 CPU 處理輸送量的量值，保證可供單一適用於 MySQL 的 Azure 資料庫伺服器使用。 計算單位是 CPU 和記憶體資源的混合量值。 通常，50 個計算單位等於半雙核心、100 個計算單位等於一個核心，而 2000 個計算單位等於 20 個保證可供您伺服器使用之處理輸送量的核心。

每個計算單位的記憶體數量均會針對基本、標準和進階服務層進行最佳化。 藉由提升效能等級來使計算單位數加倍，等於讓該單一適用於 MySQL 的 Azure 資料庫可用的資源集合加倍。

例如，標準的 2000 個計算單位所提供的 CPU 輸送量與記憶體，比使用 100 個計算單位設定的標準多 20 倍。 不過，儘管相較於基本的 100 個計算單位，標準的 100 個計算單位會提供相同的 CPU 輸送量，但是，標準層中預先設定的記憶體量是針對基本層所設定之記憶體量的兩倍，因此，可提供更好的工作負載效能與交易延遲。

>[!IMPORTANT]
>對於可預測的工作負載效能輸送量和高使用者並行處理，強烈建議您選擇標準服務層。

您隨時都可變更[服務層](./concepts-service-tiers.md)，而且幾乎不會產生應用程式停機時間。 對於許多企業和應用程式而言，能夠在每部單一適用於 MySQL 的 Azure 資料庫伺服器內建立一到多個資料庫，以及依需求調高或調低效能，可提供管理成本所需的彈性。

>[!IMPORTANT]
>我們目前支援在服務層內相應增加/減少效能等級。 例如，您可以從標準的 100 個計算單位相應增加為標準的 400 個計算單位。 同樣地，您可以從標準的 400 個計算單位相應減少為標準的 100 個計算單位。 未來將提供能夠跨服務層 (例如在基本與標準層之間) 相應增加或減少的功能。

## <a name="what-are-storage-units"></a>什麼是儲存體單位？
儲存體單位是已佈建之儲存體容量的量值，保證可供單一適用於 MySQL 的 Azure 資料庫伺服器使用。 每個服務層都有固定數量的已佈建儲存體，其包含於所選取之服務層的價格內。

(選擇性) 儲存體單位可以獨立調整，而不依賴計算單位，以 125 GB 遞增，最多可達允許的儲存體上限，如下表所述。

| **服務層功能** | **基本** | **標準** | **進階\*** |
|---------------------------|-----------|--------------|---------------|
| 內含的儲存體單位 | 50 GB | 125 GB | 250 GB |
| 總儲存體上限 | 1050 GB | 10000 GB | 4000 GB |
| 儲存體 IOPS 保證 | N/A | 是 | 是 |
| 儲存體 IOPS 上限 | N/A | 30,000 | 40,000 |

標準和進階服務層也會提供已佈建的 IOPS 保證。 可用的已佈建 IOPS 數量取決於服務層和所設定的儲存體容量。 針對部署於標準層的伺服器，IOPS 會固定為已佈建儲存體的 3 倍。 

例如，如果您的已佈建儲存體為 125 GB，則可供您伺服器使用的已佈建 IOPS 為 375。 進階層提供非常低延遲且高 IOPS 的儲存體。 針對部署於進階層的伺服器，已佈建的低延遲 IOPS 可以在已佈建儲存體的五和十倍之間進行調整。

>[!IMPORTANT]
>如果您的工作負載因已設定的計算單位而遇到瓶頸，您可能無法完全實現可用的已佈建 IOPS。 建議您監視計算單位，並在您無法完全利用可用的已佈建 IOPS 時考慮調整計算單位。

藉由遞增已佈建儲存體來調整儲存體單位，相當於按比例增加適用於標準與進階層的已佈建 IOPS。

>[!IMPORTANT]
>基本層不提供 IOPS 保證。

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>如何判斷我的工作負載所需的計算單位數？
如果您打算移轉現有的內部部署或在虛擬機器上執行的 MySQL，可透過評估工作負載需要多少個處理輸送量核心來決定計算單位數。 

如果內部部署或虛擬機器目前使用 4 個核心 (不含計算 CPU 超執行緒)，您一開始可為適用於 MySQL 的 Azure 資料庫設定 400 個計算單位。 計算單位可根據您的工作負載需求，動態地相應增加或減少，而且幾乎不會產生任何應用程式停機時間。 您也可以透過入口網站或 CLI 來監視計算單位的耗用量，以便正確調整適用於 MySQL 伺服器的資源大小。

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>如何判斷我的工作負載所需的儲存體單位數？
您可以藉由先決定所需的儲存體容量數，來估計所需的儲存體單位數。 基本、標準和進階層隨附內建於 SKU 的內含儲存體。

第二個重要因素是決定所需的 IOPS。 基本層提供變動的 IOPS，但不提供保證。 標準層會針對已佈建的儲存體以每個 GB 三 IOPS 的固定比例來調整，並提供 IOPS 保證。 您也可以透過入口網站或 CLI 監視已佈建 IOPS 的耗用量來決定使用量。

>[!IMPORTANT]
>儲存體單位一旦佈建之後，就無法動態相應減少。

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>當我達到計算單位及/或儲存體單位上限時，會發生什麼狀況？
效能層級會受校正和管理，以提供所需資源來將您的資料庫工作負載執行到您所選服務層/效能層級允許的上限。

如果您的工作負載達到計算單位/已佈建 IOPS 限制的其中一項限制，您還是能夠繼續接收最大允許層級的資源，但可能會經歷較長的查詢延遲。 這些限制並不會導致任何錯誤，但除非是速度慢到使查詢開始逾時，否則會使工作負載速度變慢。

如果您到達允許的最大連接數目限制，則您會看到明確的錯誤。 如需資源限制的詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits)。 <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>後續步驟
[適用於 MySQL 的 Azure 資料庫服務層](./concepts-service-tiers.md) 

