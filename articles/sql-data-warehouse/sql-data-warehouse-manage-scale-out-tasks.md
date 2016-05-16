<properties
   pageTitle="Azure SQL 資料倉儲 (Azure 入口網站) 的管理延展性工作 | Microsoft Azure"
   description="針對 Azure SQL 資料倉儲相應放大效能的 Azure 入口網站工作。調整 DWU 以變更計算資源。或者，暫停和繼續計算資源以節省成本。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 資料倉儲 (Azure 入口網站) 的管理延展性工作

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-scalability.md)
- [入口網站](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


彈性相應放大計算資源和記憶體，以滿足不斷變動的工作負載，並在非尖峰時間相應縮減資源以節省成本。

這個工作集合會使用 Azure 入口網站來：

- 藉由調整 DWU 來調整效能
- 暫停計算資源
- 繼續計算資源

若要了解這個問題，請參閱[效能延展性概觀][]。

<a name="scale-performance-bk"></a>

## 調整效能

[AZURE.INCLUDE [SQL 資料倉儲調整 DWU 描述](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更計算資源︰

1. 開啟 [Azure 入口網站][]，開啟您的資料庫，然後按一下 [調整]。

    ![按一下 [調整]][1]

1. 在 [調整] 刀鋒視窗中，將滑桿向左或右移動來變更 DWU 設定。

    ![移動滑桿][2]

1. 按一下 [儲存]。確認訊息隨即出現。按一下 [是] 以確認或 [否] 以取消。

    ![按一下 [儲存])][3]

<a name="pause-compute-bk"></a>

## 暫停計算

[AZURE.INCLUDE [SQL 資料倉儲暫停描述](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫︰

1. 開啟 [Azure 入口網站][]，並開啟您的資料庫。請注意，狀態為**線上**。 

    ![線上狀態][6]

1. 若要暫停計算和記憶體資源，請按一下 [暫停]，然後就會顯示確認訊息。按一下 [是] 以確認或 [否] 以取消。

    ![確認暫停][7]

1. SQL 資料倉儲啟動資料庫時，狀態會是「暫停中」。
2. 當狀態為**已暫停**，暫停作業即完成，不會再向您收取 DWU 的費用。

    ![暫停狀態][4]

<a name="resume-compute-bk"></a>

## 繼續計算

[AZURE.INCLUDE [SQL 資料倉儲繼續描述](../../includes/sql-data-warehouse-resume-description.md)]若要繼續資料庫︰

1. 開啟 [Azure 入口網站][]，並開啟您的資料庫。請注意，狀態為**已暫停**。 

    ![暫停資料庫][4]

1. 若要繼續資料庫，請按一下 [開始]，然後會顯示確認訊息。按一下 [是] 以確認或 [否] 以取消。

    ![確認繼續][5]

1. SQL 資料倉儲啟動資料庫時，狀態會是「繼續中」。
2. 當狀態變成**線上**，資料庫就已就緒。

    ![線上狀態][6]

<a name="next-steps-bk"></a>

## 後續步驟
如需詳細資訊，請參閱[管理概觀][]。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[效能延展性概觀]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[Azure 入口網站]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->