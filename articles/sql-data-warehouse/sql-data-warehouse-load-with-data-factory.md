---

title: "將資料載入 Azure SQL 資料倉儲 – Data Factory | Microsoft Docs"
description: "本教學課程使用 Azure Data Factory 將資料載入 Azure SQL 資料倉儲，並使用 SQL Server 資料庫作為資料來源。"
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b46ac604c10a2cb014991f3707fc3fc3b300f772
ms.openlocfilehash: 5b045ed236771919ea3f644a6b2351d54c75549b


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>使用 Data Factory 將資料載入 SQL 資料倉儲

本教學課程使用 Azure Data Factory 將資料載入 Azure SQL 資料倉儲，並使用 SQL Server 資料庫作為資料來源。 當您完成時，在 SQL 資料倉儲中有您的資料。

**預估時間**︰一旦符合先決條件後，本教學課程需要大約 10-15 分鐘才能完成。

## <a name="prerequisites"></a>必要條件

- 本教學課程假設您了解使用 Transact-SQL 建立資料表和結構描述的基本概念。  

- 您需要 Azure 儲存體帳戶。 您可以[申請免費 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)或[啟用 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 

- 您需要線上 SQL 資料倉儲。 如果您還沒有資料倉儲，請了解如何[建立 Azure SQL 資料倉儲](sql-data-warehouse-get-started-provision.md)。 為了達到最佳效能，在相同的 Azure 區域中找出儲存體帳戶和資料倉儲。

- 藉由建立一或多個資料表結構描述，來準備您的資料倉儲以接收內送資料。 您可以使用 [SQL 資料倉儲移轉公用程式](sql-data-warehouse-migrate-migration-utility.md)建立結構描述的指令碼。 

## <a name="configure-a-new-data-factory"></a>設定新的 Data Factory
1. 登入 [Azure 入口網站][]。
2. 找出您的資料倉儲，並按一下以開啟它。 
3. 在 [屬性] 刀鋒視窗中，按一下 [資料載入] > [Azure Data Factory]。

    ![啟動載入資料精靈](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. 您會看到 [新增 Data Factory] 對話方塊。 填寫必要的資訊，或選擇現有的 Data Factory。 按一下 [建立] 。

5. 在 [選取 Data Factory] 對話方塊中，預設會選取 [載入資料] 選項。 按一下 [下一步] 完成建立 Data Factory。 

## <a name="configure-the-data-factory-properties"></a>設定 Data Factory 屬性
現在您已建立 Data Factory，下一個步驟是設定資料載入排程。 

1. 選取 [屬性]，並填入所要求的資訊。
2. 針對 [工作名稱]，輸入 [DWLoadData fromSQLServer]。
3. 按 [下一步] 。

    ![設定負載排程](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-data-factory-source-and-gateway"></a>設定 Data Factory 來源和閘道
現在您要通知 Data Factory 關於您要載入資料的內部部署 SQL Server 資料庫。

1. 按一下 [來源]。
2. 從支援的來源資料儲存類別目錄選擇 **SQL Server**，並按 [下一步]。

    ![選擇 SQL Server 來源](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

3. 隨即出現 [指定內部部署 SQL Server 資料庫] 對話方塊。 填寫必要欄位，如下所示︰

    - **連線名稱**︰為您的連線指定新名稱。
    - **伺服器名稱**︰內部部署 SQL Server 的名稱。
    - **資料庫名稱**：SQL Server 資料庫。
    - **認證加密**︰無。 
    - **驗證類型**︰選擇您所使用的驗證類型。
    - **使用者名稱**和**密碼**︰輸入擁有複製資料權限之使用者的使用者名稱和密碼。

4. 最後一個欄位會要求閘道的名稱。 按一下 [建立閘道] 連結以建立資料管理閘道。 閘道是一個用戶端代理程式，您必須在內部部署環境中部署此代理程式，才能在內部部署與雲端資料存放區之間複製資料。 

5. 隨即出現 [建立閘道] 對話方塊。 針對名稱，輸入 **GatewayForDWLoading**，然後按一下 [建立]。

6. 隨即出現 [設定閘道] 對話方塊。  
    ![啟動快速安裝](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

7. 按一下 [啟動這台電腦上的快速安裝] 以下載、安裝及註冊目前電腦上的閘道。 進度會顯示在快顯視窗中。

    快速安裝適用於原生 Microsoft Edge 及 Internet Explorer。 如果您是使用 Google Chrome，先從 Chrome 線上應用程式商店安裝 ClickOnce 擴充功能。 或者，您可以手動下載並安裝閘道，然後使用金鑰註冊。

8. 等候閘道安裝程式完成。 一旦閘道成功註冊且上線後，快顯視窗會關閉，且新的閘道會出現在 [閘道] 欄位中。 按 [下一步] 。

9. 下一步是選擇要從中複製資料的資料表。 您可以使用關鍵字篩選資料表。 然後您可以預覽底部面板中的資料和資料表結構描述。 在完成您的選擇之後，按 [下一步]。

    ![選取資料表](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>設定目的地，您的 SQL 資料倉儲

1. 按一下 [目的地]。 您的 SQL 資料倉儲連接資訊會自動填入。
2. 輸入使用者名稱的密碼。 然後按 [下一步]。

    ![設定目的地](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

3. 智慧型資料表對應會根據類似的名稱顯示該對應來源至目的地資料表。 AD
4.  任何資料表和其他的對應會從範例中學習來自動對應。 
5. 檢閱並按 [下一步]。

    ![對應資料表](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

6. 檢閱結構描述對應，並尋找的錯誤訊息。 智慧型對應是根據資料行名稱。 如果在來源和目的地資料行之間有不支援的資料類型轉換，您會看到一則錯誤訊息與對應的資料表。

    ![對應結構描述](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

7. 按 [下一步] 。

## <a name="configure-the-performance-settings"></a>設定效能設定
在效能組態中，您要在用於暫存資料的 Azure 儲存體帳戶載入 SQL 資料倉儲之前對其進行設定。

1. 按一下 [效能]。 
2. 選取現有的 Azure 儲存體帳戶，然後按一下 [下一步]。

    ![設定預備 blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>檢閱摘要資訊和部署管線

1. 按一下 [摘要] 並檢閱資訊。
2. 按一下 [完成] 按鈕部署管線。

    ![部署 Data Factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>監視資料載入進度

部署完成之後，左側功能表上會出現 [部署] 選項。 

1. 按一下 [部署]。
2. 若要監視資料載入進度，請按一下顯示 [按一下這裡以監視複製管線] 的連結。

    ![監視管線](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

3. 在**資源總管**中，展開管線節點，然後按一下 [**DWLoadData-fromSQL]
4. 您在本教學課程中所建立的伺服器** 資料載入管線。

    ![檢視管線](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

5. 按一下以進入管線，查看對應至活動之每個資料表的詳細狀態。

    ![檢視資料表活動](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

6. 進一步按一下進入活動，您會在右窗格中看到資料載入詳細資料，包括資料大小、資料列、輸送量等。

    ![檢視資料表活動詳細資料](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

7. 若要啟動此更新，請移至您的 SQL 資料倉儲，按一下 [資料載入] > [Azure Data Factory]，選取您的 Factory，然後選擇 [監視現有的載入工作]。

## <a name="next-steps"></a>後續步驟

若要將資料庫移轉至 SQL 資料倉儲，請參閱[移轉概觀](sql-data-warehouse-overview-migrate.md)。

若要深入了解 Azure Data Factory 複製功能，請參閱 [Azure Data Factory 簡介](../data-factory/data-factory-introduction.md)和[使用複製活動移動資料](../data-factory/data-factory-data-movement-activities.md)。

若要瀏覽 SQL 資料倉儲中的資料，請參閱[使用 Visual Studio 和 SSDT 連接到 SQL 資料倉儲](sql-data-warehouse-query-visual-studio.md)和[採用 Power BI 的視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

<!-- Azure references -->
[Azure 入口網站]: https://portal.azure.com 


<!--HONumber=Nov16_HO4-->


