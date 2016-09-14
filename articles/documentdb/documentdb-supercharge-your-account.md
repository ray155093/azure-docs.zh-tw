<properties 
	pageTitle="增加 DocumentDB S1 帳戶的輸送量 | Microsoft Azure" 
	description="在 Azure 入口網站中進行一些簡單的變更，以利用 DocumentDB S1 帳戶的增加輸送量。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="mimig"/>

# 增加 DocumentDB 帳戶的輸送量

請依照下列步驟來利用 Azure DocumentDB S1 帳戶的增加輸送量。幾乎不需要額外成本，您就可以將現有 S1 帳戶的輸送量從250 [RU/s](documentdb-request-units.md) 增加到 400 RU/s 以上！

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## 在 Azure 入口網站中變更使用者定義的效能

1. 在瀏覽器中導覽至 [**Azure 入口網站**](https://portal.azure.com)。
2. 按一下 [瀏覽] -> [DocumentDB (NoSQL)]，然後選取要修改的 DocumentDB 帳戶。
3. 在 [資料庫] 功能濾鏡中選取要修改的資料庫，然後在 [資料庫] 刀鋒視窗中選取定價層為 S1 的集合。

      ![[資料庫] 刀鋒視窗與 S1 集合的螢幕擷取畫面](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. 在 [集合] 刀鋒視窗中，依序按一下 [更多] 和 [設定]。
5. 在 [設定] 刀鋒視窗中按一下 [定價層]，並請注意將會顯示每個方案的每月預估成本。在 [選擇定價層] 刀鋒視窗中按一下 [標準]，然後按一下 [選取] 以儲存變更。

      ![DocumentDB 設定和 [選擇定價層] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. 回到 [設定] 刀鋒視窗中，[定價層] 已變更為 [標準]，並且 [輸送量 (RU/秒)] 方塊會顯示預設值 400。按一下 [確定] 以儲存變更。

    > [AZURE.NOTE] 您可以將輸送量設定為介於 400 到 10,000 個[要求單位](../articles/documentdb/documentdb-request-units.md)/秒 (RU/秒)。頁面底部的 [定價摘要] 會自動更新以提供每月成本估計。
    
	![[設定] 刀鋒視窗的螢幕擷取畫面，其中顯示可供變更輸送量值的位置](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. 回到 [資料庫] 刀鋒視窗中，您可以確認已增加集合的輸送量。

	![[資料庫] 刀鋒視窗與修改後集合的螢幕擷取畫面](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

如需與使用者定義的輸送量和預先定義的輸送量相關變化的詳細資訊，請參閱部落格文章 [DocumentDB: Everything you need to know about using the new pricing options (DocumentDB：新價格選項的所有使用須知)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)。

## 後續步驟

如果您判斷您需要更多輸送量 (大於 10,000 RU/秒) 或更多儲存體 (大於 10 GB)，您可以建立資料分割的集合。若要建立資料分割的集合，請參閱[建立集合](documentdb-create-collection.md)。

<!---HONumber=AcomDC_0831_2016-->