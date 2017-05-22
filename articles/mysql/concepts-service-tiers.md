---
title: "適用於 MySQL 的 Azure 資料庫中的服務層 | Microsoft Docs"
description: "適用於 MySQL 的 Azure 資料庫中的服務層"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b647db3c3a48ac6c151814ee68b3117a92c1d4d8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-service-tier"></a>適用於 MySQL 的 Azure 資料庫選項和效能：了解每個服務層中可用的項目
適用於 MySQL 的 Azure 資料庫提供基本和標準服務層。 尚未提供進階服務層。

每個服務層都有多個效能等級，可處理不同類型的工作負載需求。 較高的效能等級會提供額外的資源，旨在提供愈來愈高的輸送量。 您可以在服務層內動態變更效能等級，而不需使應用程式停機。

未來將能從某個服務層升級或降級至另一個服務層。 

> [!IMPORTANT]
> 服務目前處於公開預覽狀態，因此尚未提供服務等級協定 (SLA)。

您可以在特定效能等級的服務層內，建立具有專用資源的單一適用於 MySQL 的 Azure 資料庫伺服器。 接著可於要在多個資料庫上共用資源的伺服器內建立一到多個資料庫。 適用於單一適用於 MySQL 的 Azure 資料庫伺服器的資源，會以計算單位和儲存體單位來表示。 如需計算單位和儲存體的詳細資訊，請參閱[說明計算單位和儲存體單位](concepts-compute-unit-and-storage.md)

## <a name="choosing-a-service-tier"></a>選擇服務層

下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
| :----------- | :----------------|
| 基本 | 最適合需要可調整計算和儲存體而不需 IOPS 保證的小型工作負載。 範例包括用於開發或測試的伺服器，或者不常使用的小規模應用程式。 |
| 標準 | 適用於需要 IOPS 保證之雲端應用程式的進階選項，能夠獨立調整到更高的計算與儲存體，以獲取高輸送量。 範例包括 Web 或分析應用程式。 |
| 進階 | 最適合需要為交易和 IO 提供非常短暫之延遲的工作負載，以及高 IO 和工作負載輸送量。 可為許多並行使用者提供最佳支援。 適用於支援任務關鍵性應用程式的資料庫。<br />預覽版中並未提供進階服務層。 |
| &nbsp; | &nbsp; |

若要決定服務層，請先從判斷您的工作負載是否需要 IOPS 保證開始。 接著，決定您所需的基本功能：

| **服務層功能** | **基本** | **標準** | **進階** * |
| :------------------------ | :-------- | :----------- | :------------ |
| 計算單位數目上限 | 100 | 2000 | 預覽版中並未提供 |
| 總儲存體上限 | 1050 GB | 10000 GB | 預覽版中並未提供 |
| 儲存體 IOPS 保證 | N/A | 是 | 預覽版中並未提供 |
| 儲存體 IOPS 上限 | N/A | 30,000 | 預覽版中並未提供 |
| 資料庫備份的保留期限 | 7 天 | 35 天 | 35 天 |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |

> [!NOTE]
> 預覽版中的標準服務層目前最多支援 800 個計算單位，以及 1000 GB 的儲存體上限。

一旦決定最低服務層之後，接下來要決定適用於 MySQL 的 Azure 資料庫伺服器的效能等級 (計算單位)。 標準的 200 和 400 個計算單位，對於其 Web 或分析工作負載需要更高使用者並行處理的應用程式而言，通常是個良好的起點。 

不過，您可以根據工作負載的需求，單獨相應增加或減少計算單位，而不依賴儲存體單位。 如果工作負載需要調整計算資源，您可以動態增加或減少計算單位。 如果您的工作負載需要更多的 IOPS 或儲存體，則您也可以調整儲存體。

> [!NOTE]
> 在預覽版中，基本和標準層目前不支援動態調整儲存體。 我們計劃在未來新增此功能。

> [!NOTE]
> 在標準服務層，IOPS 會以固定 3:1 的比例，按比例調整已佈建的儲存體大小。 125 GB 的內含儲存體可為 375 已佈建的 IOPS 提供保證，每個的 IO 大小可高達 256 KB。 如果您佈建 1000 GB，您將會取得 3000 已佈建的 IOPS。 您必須監視伺服器計算單位耗用量並相應增加，以充分利用可用的已佈建 IOPS。

## <a name="service-tiers-and-performance-levels"></a>服務層和效能等級

適用於 MySQL 的 Azure 資料庫可在每個服務層內提供多個效能等級。 您可以使用下列其中一種方式，彈性選擇最符合您工作負載需求的等級：

- [Azure 入口網站](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

無論每部 MySQL 伺服器內裝載的資料庫數目如何，您的資料庫都保證會有一組資源，且您伺服器的預期效能特性不會受到影響。

基本服務層：

| **效能等級** | **50** | **100** |
| :-------------------- | :----- | :------ |
| 計算單位數目上限 | 50 | 100 |
| 內含的儲存體大小 | 50 GB | 50 GB |
| 伺服器儲存體大小上限\* | 1050 GB | 1050 GB |
| 並行登入數上限 | &nbsp; | &nbsp; |
| 連接數目上限 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; |

標準服務層：

| **效能等級** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| 計算單位數目上限 | 100 | 200 | 400 | 800 |
| 內含的儲存體大小和已佈建的 IOPS | 125 GB，375 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 伺服器儲存體大小上限\* | 1 TB | &nbsp; | &nbsp; | &nbsp; |
| 伺服器佈建的 IOPS 上限 | 3000 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 每個 GB 中伺服器佈建的 IOPS 上限 | 每個 GB 中固定 3 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 並行登入數上限 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| 連接數目上限 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |

\* 伺服器儲存體大小上限是指針對您伺服器佈建的儲存體大小上限。

## <a name="scaling-up-or-down-a-single-server"></a>相應增加或減少單一伺服器

一開始選擇服務層和效能等級之後，您就能根據工作負載需求，動態相應增加或減少伺服器。 如果您需要相應增加或減少，使用 Azure 入口網站或 Azure CLI，即可輕鬆變更資料庫層級。

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。 如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。

整個相應增加程序的期間取決於伺服器變更前後的大小和服務層。 例如，在標準服務層內變更計算單位的伺服器應在幾分鐘內完成。 完成變更之前，不會將新屬性套用至伺服器。

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>進行相應增加或減少之步驟的相關文件

- [使用 Azure 入口網站管理單一伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 管理單一伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)

### <a name="details-about-scaling-up-or-down"></a>相應增加或減少的相關詳細資料

- 若要將資料庫降級，伺服器儲存體單位應該小於目標服務層允許的大小上限。
- 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱[如何使用 Azure 入口網站來備份和還原適用於 MySQL 的 Azure 資料庫伺服器](./howto-restore-server-portal.md)
- 完成變更之前，不會將新屬性套用至伺服器。
