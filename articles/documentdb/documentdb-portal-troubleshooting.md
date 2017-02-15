---
title: "為 DocumentDB 入口網站問題進行疑難排解 | Microsoft Docs"
description: "了解如何解決 DocumentDB Azure 入口網站中的問題。"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 36ad9bf4-2617-4347-ba29-edebf62fc3ec
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d41cbded31d64525a3e04ef742091c8d87760416


---
# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Azure DocumentDB 入口網站疑難排解秘訣
本文說明如何解決 Azure 入口網站中的 DocumentDB 問題。 

## <a name="resources-are-missing"></a>資源遺失
**徵兆**︰您入口網站刀鋒視窗中的資料庫或集合遺失。

**解決方案**︰降低應用程式使用量，使其可在集合的最大傳輸量配額下運作。 

**說明**︰此入口網站是一個應用程式，就像所有其他應用程式一樣，可呼叫您的 DocumentDB 資料庫和集合。 如果您的要求目前因為個別應用程式所進行的呼叫而受到節流處理，入口網站也可能會受到節流處理，因而導致資源不會在入口網站中顯示。 若要解決此問題，請先解決導致出現高輸送使用量的原因，然後重新整理入口網站刀鋒視窗。 如需有關如何測量及降低輸送使用量的資訊，請參閱[效能祕訣](documentdb-performance-tips.md)一文的[輸送量](documentdb-performance-tips.md#throughput)一節。

## <a name="pages-or-blades-wont-load"></a>無法載入頁面或刀鋒視窗
**徵兆**︰入口網站中的頁面和刀鋒視窗沒有顯示。

**解決方案**︰降低應用程式使用量，使其可在集合的最大傳輸量配額下運作。 

**說明**︰此入口網站是一個應用程式，就像所有其他應用程式一樣，可呼叫您的 DocumentDB 資料庫和集合。 如果您的要求目前因為個別應用程式所進行的呼叫而受到節流處理，入口網站也可能會受到節流處理，因而導致資源不會在入口網站中顯示。 若要解決此問題，請先解決導致出現高輸送使用量的原因，然後重新整理入口網站刀鋒視窗。 如需有關如何測量及降低輸送使用量的資訊，請參閱[效能祕訣](documentdb-performance-tips.md)一文的[輸送量](documentdb-performance-tips.md#throughput)一節。

## <a name="add-collection-button-is-disabled"></a>[加入集合] 按鈕已停用
**徵兆︰**在 [資料庫] 刀鋒視窗中，[新增集合] 按鈕已停用。

**說明**︰如果您的 Azure 訂用帳戶是與權益信用額度相關聯 (例如，MSDN 訂用帳戶所提供的免費信用額度)，而您已使用了該月份的所有信用額度，您就無法在 DocumentDB 中建立任何其他集合。

**解決方案**︰從您的帳戶移除消費限制。

1. 在 Azure 入口網站的 Jumpbar 中按一下 [訂用帳戶]、按一下與 DocumentDB 資料庫相關聯的訂用帳戶，然後在 [訂用帳戶] 刀鋒視窗中，按一下 [管理]。 
    ![DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. 在新的瀏覽器視窗中，您將會看到您沒有任何剩餘的信用額度。 按一下 [移除消費限制]  按鈕，以移除僅目前計費週期的消費或無限期移除消費。 接著完成精靈，以新增或確認您的信用卡資訊。 
    ![DocumentDB 提供多個定義完善 (寬鬆) 的一致性模型，讓您可從中選擇](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## <a name="query-explorer-completes-with-errors"></a>[查詢總管] 完成但發生錯誤
請參閱 [為查詢總管進行疑難排解](documentdb-query-collections-query-explorer.md#troubleshoot)。

## <a name="no-data-available-in-monitoring-tiles"></a>監視圖格中沒有可用的資料
請參閱 [為監視圖格進行疑難排解](documentdb-monitor-accounts.md#troubleshooting)。

## <a name="no-documents-returned-in-document-explorer"></a>[文件總管] 中未傳回任何文件
請參閱 [為文件總管進行疑難排解](documentdb-view-json-document-explorer.md#troubleshoot)。

## <a name="next-steps"></a>後續步驟
如果您仍會在入口網站中發生問題，請寄送電子郵件到 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) 以尋求協助，或在入口網站中，依序按一下 [瀏覽]、[說明 + 支援] 及 [建立支援要求] 來提出支援要求。




<!--HONumber=Nov16_HO3-->


