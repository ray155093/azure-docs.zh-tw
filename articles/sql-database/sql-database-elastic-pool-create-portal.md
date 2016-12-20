---
title: "使用 Azure 入口網站建立新的彈性集區 | Microsoft Docs"
description: "如何將可調整的彈性資料庫集區新增至 SQL Database 組態，以便更輕鬆地進行多個資料庫的系統管理與資源共用。"
keywords: "可調整資料庫,資料庫組態"
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: 
ms.assetid: bf12594b-d258-40e6-a9fc-d8a8710c2d65
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: hero-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 1a0b8609acd99ec188f92a32ed4cb44a68edc3b2
ms.openlocfilehash: 54deca0518d82de59a58e02fc38693179c486b64


---
# <a name="create-a-new-elastic-database-pool-with-the-azure-portal"></a>使用 Azure 入口網站來建立新的彈性資料庫集區
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>

本文將說明如何透過 [Azure 入口網站](https://portal.azure.com/)建立可調整的[彈性集區](sql-database-elastic-pool.md)。 集區的建立方式有兩種。 如果您知道您要的集區設定則可從頭開始進行，或從服務的建議著手。 SQL Database 擁有的內建智慧功能會根據您資料庫過去的使用狀況遙測，為您推薦更符合成本效益的集區設定。

您可以將多個集區新增至伺服器，但無法將來自不同伺服器的資料庫新增到相同的集區。 若要建立集區，您至少需要一個位於 V12 伺服器的資料庫。 如果沒有這樣的資料庫，請參閱 [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。 您可以建立只有一個資料庫的集區，但具有多個資料庫的集區才符合成本效益。 請參閱 [彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

> [!NOTE]
> 彈性集區已在所有 Azure 區域中正式運作 (GA)，但印度西部除外，此區域目前提供預覽版。  我們將儘速在此區域提供彈性集區的 GA。
>
>

## <a name="step-1-create-a-new-pool"></a>步驟 1：建立新的集區

本文說明如何從入口網站中現有的 **伺服器** 刀鋒視窗建立新的集區，這是將現有資料庫移到集區中的最簡單方式。

> [!NOTE]
> 您也可以在 [Marketplace] 中搜尋 **SQL 彈性集區**，或按一下 [SQL 彈性集區] 瀏覽刀鋒視窗中的 [+新增]，以建立新的集區。 您可以透過此集區佈建工作流程來指定新的或現有的伺服器。
>
>

1. 在 [Azure 入口網站](http://portal.azure.com/)左側的清單底下，按一下 [更多服務] **>** [SQL 伺服器]，然後按一下要新增集區之資料庫所在的伺服器。
2. 按一下 [新增集區] 。

    ![將集區新增到伺服器](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-或-**

    您可能會看到一則訊息，表示伺服器有建議的彈性集區 (僅限 V12)。 按一下訊息以查看系統根據資料庫的歷史使用量遙測資料所推薦的集區，然後按一下定價層以查看更多詳細資料並自訂集區。 若要了解系統是如何做出推薦的，請參閱本主題稍後的 [了解集區建議](#understand-pool-recommendations) 。

    ![建議的集區](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. [彈性資料庫集區] 刀鋒視窗隨即出現，您可以在其中指定集區的設定。 如果您在上個步驟中按下 [新增集區]，定價層預設將會是**標準**，而且尚未選取任何資料庫。 您可以建立空的集區，或指定一組該伺服器的現有資料庫以移入集區。 如果您正在建立建議的集區，即會預先填入建議的定價層、效能設定及資料庫清單，但您還是可以變更它們。

    ![設定彈性集區](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. 指定彈性集區的名稱，或保留預設值。

## <a name="step-2-choose-a-pricing-tier"></a>步驟 2：選擇定價層

集區的定價層決定了集區中彈性資料庫可用的功能，還有最大數目的 eDTU (eDTU MAX)，以及每個資料庫可用的儲存體 (GB)。 如需詳細資訊，請參閱服務層。

若要變更集區的定價層，請依序按一下 [定價層]、您想要的定價層及 [選取]。

> [!IMPORTANT]
> 在最後一個步驟中選擇好定價層並按一下 [確定] 認可變更後，您就無法再變更集區的定價層。 若要變更現有彈性集區的價格層，在所需的價格層中建立新的彈性集區，並且將彈性資料庫移轉至這個新的集區。
>
>

![選取定價層](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## <a name="step-3-configure-the-pool"></a>步驟 3︰設定集區

設定定價層之後，請按一下 [設定集區]，即可新增資料庫、設定集區 eDTU 和儲存體 (集區 GB)，以及設定集區中彈性資料庫的最大和最小 eDTU。

1. 按一下 [設定集區] 
2. 選取您要新增至集區的資料庫。 建立集區時，這個步驟是選擇性步驟。 建立集區後，可以新增資料庫。
    若要新增資料庫，請依序按一下 [新增資料庫]、您想要新增的資料庫以及 [選取] 按鈕。

    ![新增資料庫](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    如果您要使用的資料庫有足夠的歷史使用量遙測資料，[預估的 eDTU 和 GB 使用量] 圖形和 [實際的 eDTU 使用量] 長條圖便會更新以幫助您決定要使用的組態。 此外，該服務可能會提供您建議訊息，協助您決定集區的適當大小。 請參閱 [動態建議](#dynamic-recommendations)。

3. 使用 [設定集區]  頁面上的控制項，探索設定及設定您的集區。 如需各服務層限制的詳細資訊，請參閱[彈性集區限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)；如需如何決定集區適當大小的詳細指引，則請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。 如需集區設定的詳細資訊，請參閱[彈性集區屬性](sql-database-elastic-pool.md#elastic-pool-and-elastic-database-properties)。

    ![設定彈性集區](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. 變更設定之後，請按一下 [設定集區] 刀鋒視窗中的 [選取]。
5. 按一下 [確定]  以建立集區。


## <a name="understand-pool-recommendations"></a>了解集區建議

SQL Database 服務會評估使用量的歷史資料，並為您推薦一或多個比使用單一資料庫更符合成本效益的集區。 每個推薦集區都是以最適合該集區的伺服器資料庫唯一子集進行設定。

![建議的集區](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

集區建議包含下列內容︰

- 集區的定價層 (基本、標準或進階)。
- 適當的 [集區 eDTU]  (也稱為每一集區的最大 eDTU)
- 每一資料庫的 [eDTU 上限] 和 [eDTU 下限]
- 集區的建議資料庫清單

服務在建議集區時，會計算過去 30 天的遙測。 對於被視為彈性資料庫集區候選項目的資料庫，它必須存在至少 7 天。 已在彈性資料庫集區中的資料庫不會被視為彈性資料庫集區建議候選項目。

服務會評估將每個服務層中的單一資料庫移至同一層集區的資源需求和成本效益。 例如，會評估伺服器上的所有 Standard 資料庫是否適合 Standard 彈性集區。 這表示服務不會進行跨層建議，例如將 Standard 資料庫移到 Premium 集區。

### <a name="dynamic-recommendations"></a>動態建議

將資料庫新增至集區之後，將會根據您所選資料庫的過去使用量，動態產生建議。 這些建議將會顯示在 eDTU 和 GB 使用量圖表，以及 [設定集區]  刀鋒視窗頂端的建議橫幅中。 這些建議旨在協助您為特定的資料庫建立最最佳化集區。

![動態建議](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="additional-resources"></a>其他資源

- [使用入口網站管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-portal.md)
- [使用 PowerShell 管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-powershell.md)
- [使用 C# 管理 SQL Database 彈性集區](sql-database-elastic-pool-manage-csharp.md)
- [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)



<!--HONumber=Nov16_HO3-->


