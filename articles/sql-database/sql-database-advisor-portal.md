---
title: "使用 Azure 入口網站的 Azure SQL Database 建議程式 | Microsoft Docs"
description: "您可以在 Azure 入口網站中，使用 Azure SQL Database 建議程式來檢閱和實作適用於您現有 SQL Database 的建議，這些建議可以改善現有的查詢效能。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e16587c3eb7366c92297455e8a175ab118174526


---
# <a name="sql-database-advisor-using-the-azure-portal"></a>使用 Azure 入口網站的 SQL Database 建議程式
> [!div class="op_single_selector"]
> * [SQL Database 建議程式概觀](sql-database-advisor.md)
> * [入口網站](sql-database-advisor-portal.md)
> 
> 

您可以在 Azure 入口網站中，使用 Azure SQL Database 建議程式來檢閱和實作適用於您現有 SQL Database 的建議，這些建議可以改善現有的查詢效能。

## <a name="viewing-recommendations"></a>檢視建議
您可以在 [建議] 頁面檢視排名最前面的建議，順序是根據其在改進效能上可能產生的影響而定。 您也可以檢視歷程記錄作業的狀態。 選取建議或狀態來查看其詳細資料。

若要檢視和套用建議，您在 Azure 中必須有正確的 [角色型存取控制](../active-directory/role-based-access-control-configure.md) 權限。 需要**讀取者**、**SQL DB 參與者**權限，才能檢視建議，以及需要**擁有者**、**SQL DB 參與者**權限，才能執行任何動作；建立或卸除索引並取消建立索引。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [更多服務]  >  [SQL Database]，並選取您的資料庫。
3. 按一下 [效能建議] 來檢視適用於所選資料庫的可用建議。

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一天，而且那一天之內需要有一些活動。 也需要有一些一致的活動。 相較於隨機蹦出的零星活動，一致的查詢模式更有利於 SQL Database 建議程式最佳化。 如果 [效能建議]  頁面中沒有可用的建議，該頁面應會提供訊息說明原因。
> 
> 

![建議](./media/sql-database-advisor-portal/recommendations.png)

以下是 Azure 入口網站中「修正結構描述問題」建議的範例。

![修正結構描述問題](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

依照可能帶來的效能影響排序，建議分成下列 4 個類別：

| 影響 | 說明 |
|:--- |:--- |
| 高 |高影響建議提供最明顯的效能影響。 |
| 中型 |中度影響建議會改善效能，但不顯著。 |
| 低 |低影響建議比沒有建議時提供更好的效能，但改善可能不顯著。 |

### <a name="removing-recommendations-from-the-list"></a>從清單中移除建議
如果建議的清單中包含您想從清單中移除的項目，您可以捨棄該建議：

1. 在 [建議] 清單中選取建議。
2. 在 [詳細資料] 刀鋒視窗上按一下 [捨棄]。

如有需要，您可以將捨棄的項目加回到 **建議** 清單：

1. 在 [建議] 刀鋒視窗上按一下 [檢視已捨棄]。
2. 從清單中選取捨棄的項目以檢視其詳細資料。
3. (選擇性) 按一下 [復原捨棄]，將索引加回到**建議**的主要清單。

## <a name="applying-recommendations"></a>套用建議
SQL Database 建議程式可讓您使用下列 3 個選項的其中任一選項來控制建議的啟用方式： 

* 一次套用一個個別的建議。
* 讓建議程式自動套用建議 (目前僅適用索引建議)。
* 若要手動實作建議，請針對您的資料庫執行建議的 T-SQL 指令碼。

選取任何建議來檢視其詳細資料，然後按一下 [檢視指令碼]  來檢閱將如何建立建議的確切詳細資料。

當建議程式套用建議時，資料庫會保持上線 - 使用 SQL Database 建議程式不會使資料庫離線。

### <a name="apply-an-individual-recommendation"></a>套用個別的建議
您可以一次檢閱並接受一個建議。

1. 在 [建議] 刀鋒視窗上按一下某個建議。
2. 在 [詳細資料] 刀鋒視窗上按一下 [套用]。
   
    ![套用建議](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>啟用自動索引管理
您可以將 SQL Database 建議程式設為自動實作建議。 當建議可供使用時將會自動套用。 因為所有的索引作業都由服務管理，所以若對效能產生負面影響，就會還原該建議。

1. 在 [建議] 刀鋒視窗上按一下 [自動化]：
   
    ![建議程式設定](./media/sql-database-advisor-portal/settings.png)
2. 將建議程式設為自動**建立**或**卸除**索引：
   
    ![建議的索引](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>手動執行建議的 T-SQL 指令碼
選取任何建議，然後按一下 [檢視指令碼] 。 對資料庫執行這個指令碼，以手動套用建議。

 ，因此建議您在建立這些索引之後監視索引，以確認它們能夠提高效能，且於必要時調整或刪除它們。 如需有關建立索引的詳細資訊，請參閱 [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx)。

### <a name="canceling-recommendations"></a>取消建議
可以取消處於**擱置中**、**確認中**或**成功**狀態的建議。 狀態為 **執行中** 的建議無法取消。

1. 在 [調整歷程記錄] 區域中選取建議，以開啟 [建議詳細資料] 刀鋒視窗。
2. 按一下 [取消]  以中止套用建議的程序。

## <a name="monitoring-operations"></a>監視作業
套用建議時可能不會立即執行。 入口網站會提供有關建議作業狀態的詳細資料。 索引有下列可能的狀態：

| 狀態 | 說明 |
|:--- |:--- |
| Pending |已收到套用建議命令，且已排程執行。 |
| 執行中 |正在套用建議。 |
| 成功 |已成功套用建議。 |
| 錯誤 |套用建議程序期間發生錯誤。 這可能是暫時性問題，也可能是資料表的結構描述變更，造成指令碼不再有效。 |
| 還原 |已套用建立但被認為無助於效能，正在自動還原。 |
| 已還原 |已還原建議。 |

按一下清單中正在處理的建議以查看其詳細資料：

![建議的索引](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>還原建議
如果您使用建議程式來套用建議 (表示您未手動執行 T-SQL 指令碼)，如果建議程式發現會對效能造成負面影響，它將會自動還原建議。 如果您因為任何原因想要還原建議，您可以執行以下步驟：

1. 在 [調整歷程記錄]  區域中選取已成功套用的建議。
2. 在 [建議詳細資料] 刀鋒視窗上按一下 [還原]。

![建議的索引](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>監視索引建議的效能影響
成功實作建議之後 (目前僅提供索引作業和參數化查詢建議)，您可以按一下 [建議詳細資料] 刀鋒視窗上的 [Query Insights]  來開啟 [ [查詢效能深入解析](sql-database-query-performance.md) ]，並查看排名最前面查詢對效能的影響。

![監視效能影響](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>摘要
SQL Database 建議程式會提供可改善 SQL Database 效能的建議。 藉由提供 T-SQL 指令碼，以及獨立且全自動的 (僅適用目前的索引)，建議程式會提供協助以最佳化資料庫，並最終改善查詢效能。

## <a name="next-steps"></a>後續步驟
監視建議，並繼續套用建議以改善效能。 資料庫工作負載會動態地持續變更。 SQL Database 建議程式會繼續監視並提供可能改善資料庫效能的建議。 

* 如需了解 SQL Database 建議程式的概觀，請參閱 [SQL Database 建議程式](sql-database-advisor.md) 。
* 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解如何檢視排名最前面查詢的效能影響。

## <a name="additional-resources"></a>其他資源
* [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [角色型存取控制](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Nov16_HO3-->


