<properties
   pageTitle="將您的解決方案移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將您的解決方案帶入 Azure SQL 資料倉儲平台的移轉指導。"
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
   ms.date="08/17/2016"
   ms.author="barbkess;jrj;sonyama"/>

# 將您的解決方案移轉至 SQL 資料倉儲

SQL 資料倉儲是一個能夠彈性調整以符合您需求的分散式資料庫系統。為了維護效能與延展性，並非所有 SQL Server 內的功能都會在「SQL 資料倉儲」內實作。下列移轉主題會介紹一些將解決方案移轉到「SQL 資料倉儲」的重要因素。由於針對延展性設計資料倉儲帶來了不同的設計模式，因此傳統的方法不一定最適合。您可能會因而發現到，調整現有的解決方案可確保您能夠充分利用「SQL 資料倉儲」所提供的分散式平台。

另外也請務必記得，SQL 資料倉儲是以 Microsoft Azure 為基礎的平台。因此，移轉過程很有可能需將您的資料傳輸至雲端。資料傳輸應另行討論且必須謹慎考量，尤其是在磁碟區增加時。資料傳輸和資料載入是個別的主題。

## 移轉指導
開始移轉之前，請確定您已將這些文章都看過一遍，以確保您了解一些產品差異和基本概念。

- [遷移結構描述][]
- [遷移資料][]
- [遷移程式碼][]

## 後續步驟
如需其他開發祕訣，請參閱[開發概觀][]。

您也可以檢視 [Transact-SQL 參考資料][]，以取得更多資訊。

最後，請查看[載入概觀][]。該文章討論各種資料載入選項，並提供逐步指引。

<!--Image references-->

<!--Article references-->
[遷移結構描述]: sql-data-warehouse-migrate-schema.md
[遷移資料]: sql-data-warehouse-migrate-data.md
[遷移程式碼]: sql-data-warehouse-migrate-code.md

[開發概觀]: sql-data-warehouse-overview-develop.md
[載入概觀]: sql-data-warehouse-overview-load.md
[Transact-SQL 參考資料]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->