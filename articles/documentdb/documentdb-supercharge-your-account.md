---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>移轉您的 DocumentDB S1、S2 或 S3 帳戶
依照下列步驟來移動至標準定價層，以便利用 Azure DocumentDB 帳戶的增加輸送量。 幾乎不需要額外成本，您就可以將現有 S1 帳戶的輸送量從250 [RU/s](documentdb-request-units.md) 增加到 400 RU/s 以上！ 所有標準帳戶皆受益於可調整輸送量以符合應用程式需求的能力。 您不再需要從預先定義的輸送量選項中選擇，每當您需要達到應用程式所需的輸送量就可以調整。 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>在 Azure 入口網站中變更使用者定義的效能
1. 在瀏覽器中導覽至 [**Azure 入口網站**](https://portal.azure.com)。 
2. 按一下左側功能表中的 [NoSQL (DocumentDB)]，或按一下 [更多服務] 並捲動到 [資料庫]，然後選取 [NoSQL (DocumentDB)]。   
3. 在 [NoSQL (DocumentDB)] 刀鋒視窗中，選取要修改的帳戶。
4. 在新刀鋒視窗中[集合] 底下的功能表中，按一下 [調整]。 

      ![DocumentDB 設定和 [選擇定價層] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. 如以下螢幕擷取畫面所示，執行下列操作： 

 - 在新刀鋒視窗中，使用下拉式功能表來選取 S1、S2 或 S3 定價層的集合。 
 - 按一下 [定價層S1]、[S2] 或 [S3]。
 - 在 [選擇定價層] 刀鋒視窗中按一下 [標準]，然後按一下 [選取] 以儲存變更。
   
6. 回到 [調整] 刀鋒視窗，[輸送量 (RU/s)] 方塊會顯示預設值 400，且[定價層] 已變更為 [標準]。  您可以將輸送量變更為應用程式所需的任何值。 我們建議使用 [DocumentDB 容量規劃工具](https://www.documentdb.com/capacityplanner)來判斷您的應用程式每秒需要多少[要求單位](documentdb-request-units.md) (RU/s)。 頁面底部 [估計每月帳單] 的部分會自動更新以提供每月成本估計。 按一下 [確定] 儲存變更。 
      
    ![[設定] 刀鋒視窗的螢幕擷取畫面，其中顯示可供變更輸送量值的位置](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. 集合會調整以符合新的需求，並顯示成功訊息。  
   
    ![[資料庫] 刀鋒視窗與修改後集合的螢幕擷取畫面](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

如需與使用者定義的輸送量和預先定義的輸送量相關變化的詳細資訊，請參閱部落格文章 [DocumentDB: Everything you need to know about using the new pricing options (DocumentDB：新價格選項的所有使用須知)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)。

## <a name="next-steps"></a>後續步驟

請參閱 [Azure DocumentDB 中的資料分割和調整](documentdb-partition-data.md)，了解如何利用 DocumentDB 來分割資料並達成全域調整。



<!--HONumber=Feb17_HO2-->


