---
title: "查詢效能微調建議 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database 建議程式會提供可改善現有 SQL Database 查詢效能的建議。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & manage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: a8d0b08abc7e3c688f9ab79499b3459b33f06848
ms.contentlocale: zh-tw
ms.lasthandoff: 02/02/2017


---
# <a name="sql-database-advisor"></a>SQL Database 建議程式

Azure SQL Database 會學習和配合您的應用程式，並提供自訂的建議讓您將 SQL Database 的效能最大化。 SQL Database 建議程式提供建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。 建議程式會藉由分析 SQL 資料庫的使用狀況歷程記錄，來評估效能。 建議採用最適合用於執行您的資料庫之一般工作負載的建議事項。 

下列建議適用於 Azure SQL Database 伺服器。 您目前可以設定要自動套用建立和卸除索引建議，如需詳細資訊，請參閱 [自動索引管理](sql-database-advisor-portal.md#enable-automatic-index-management) 。

## <a name="create-index-recommendations"></a>建立索引建議
**建立索引** 建議。

## <a name="drop-index-recommendations"></a>卸除索引建議
**卸除索引** 建議。

## <a name="parameterize-queries-recommendations"></a>參數化查詢建議
當您有一或多個查詢不斷被重新編譯，但結果都是相同的查詢執行計劃時，即會出現**參數化查詢**建議。 這樣可讓您套用強制參數化，將查詢計劃加入快取，並在未來重複使用，改善效能並減少資源使用量。 

針對 SQL Server 發出的每個查詢一開始需要重新編譯，以產生執行計劃。 每個產生的計畫都會加入至計畫快取，而相同查詢的後續執行可以從快取中重複使用這個計畫，而不需要進一步編譯。 

如果應用程式所傳送查詢包含非參數化的值，則該應用程式會導致效能額外負荷，其中會針對每個這類具有不同參數值的查詢，再次編譯執行計劃。 在許多情況下，具有不同參數值的相同查詢都會產生相同執行計劃，但這些計畫仍會個別加入至計劃快取。 重新編譯執行計劃會使用資料庫資源、增加查詢持續時間，以及造成計劃快取溢位，因而導致系統從快取中回收計劃。 您可以在資料庫上設定強制參數化選項，來改變這個 SQL Server 行為。 

為了協助您評估此建議的影響，我們提供了實際 CPU 使用量和預計 CPU 使用量 (如同已套用建議) 之間的比較。 除了可節省 CPU 之外，您的查詢持續期間將會降低花費在編譯的時間。 這也可讓計畫快取中的額外負荷降低不少，讓大多數的計畫可以保留在快取中並重複使用。 您可以按一下 [套用] 命令，快速且輕鬆地套用此建議。 

一旦套用此建議之後，它將在幾分鐘內於您的資料庫上啟用強制參數化，而它將開始監視程序，此程序大約會持續 24 小時。 經過這段期間之後，您就能看到驗證報告，其中顯示資料庫在套用建議前後 24 小時的 CPU 使用量。 SQL Database 建議程式有一項安全機制，會在偵測到效能變差時，自動還原套用的建議。

## <a name="fix-schema-issues-recommendations"></a>修正結構描述問題建議
當 SQL Database 服務注意到 Azure SQL Database上發生結構描述數目異常狀況相關的 SQL 錯誤時，即會出現**修正結構描述問題**建議。 您的資料庫在一小時內遇到多個結構描述相關的錯誤時 (無效的資料行名稱、無效的物件名稱等)，通常會出現此建議。

「結構描述問題」是 SQL Server 中一個語法錯誤的類別，當 SQL 查詢定義與資料庫結構描述定義不符時即會發生此問題。 例如，查詢預期的其中一個資料行可能在目標資料表中遺失，反之亦然。 

當 Azure SQL Database 服務注意到 Azure SQL Database 上發生結構描述數目異常狀況相關的 SQL 錯誤時，即會出現「修正結構描述問題」建議。 下表顯示與結構描述問題相關的錯誤：

| SQL 錯誤碼 | 訊息 |
| --- | --- |
| 201 |程序或函數 '' 必須有參數 ''，但未提供。 |
| 207 |無效的資料行名稱 '*'。 |
| 208 |無效的物件名稱 '*'。 |
| 213 |資料行名稱或提供的數值數量與資料表定義不相符。 |
| 2812 |找不到預存程序 ' *'。 |
| 8144 |程序或函數 * 指定了太多的引數。 |

## <a name="next-steps"></a>後續步驟
監視建議，並繼續套用建議以改善效能。 資料庫工作負載會動態地持續變更。 SQL Database 建議程式會繼續監視並提供可能改善資料庫效能的建議。 

* 如需如何在 Azure 入口網站中使用 SQL Database 建議程式的步驟，請參閱 [Azure 入口網站中的 SQL Database 建議程式](sql-database-advisor-portal.md) 。
* 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解和檢視排名最前面查詢的效能影響。

## <a name="additional-resources"></a>其他資源
* [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [角色型存取控制](../active-directory/role-based-access-control-configure.md)


