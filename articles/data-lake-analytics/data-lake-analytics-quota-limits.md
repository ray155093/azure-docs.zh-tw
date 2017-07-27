---
title: "Azure Data Lake Analytics 配額限制 | Microsoft Docs"
description: "了解如何調整並提高 Azure Data Lake Analytics (ADLA) 帳戶中的配額限制。"
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 配額限制
了解如何調整並提高 Azure Data Lake Analytics (ADLA) 帳戶中的配額限制。 了解這些限制可幫助您了解 U-SQL 作業的行為。 這所有限制並非強制，您永遠可以與我們連絡來增加上限。

## <a name="azure-subscriptions-limits"></a>Azure 訂用帳戶限制

**每個訂用帳戶的 ADLA 帳戶最大數目︰**5。 這是每個訂用帳戶您可以建立的 ADLA 帳戶的數目上限。 當您嘗試建立第六個 ADLA 帳戶，您收到這個錯誤「您已達到訂用帳戶名稱下區域中允許的 Data Lake Analytics 帳戶的數目上限 (5)」。 您可以輕鬆地修正此問題，方法是刪除您的訂用帳戶下使用的 ADLA 帳戶，或透過開啟支援票證來連絡我們。

## <a name="adla-account-limits"></a>ADLA 帳戶限制

**每個帳戶分析單位 (AU) 的最大數目︰**250。 

這是可以同時在您的帳戶中執行的 AU 的最大數目。 您跨所有工作的總執行 AU 不能超過這個。 超過此值會導致將較新的工作自動加入佇列。 例如：

  * 您可能只有一個工作使用 250 個 AU 執行，當您送出第二個工作時，在第一個動作完成之前，這個工作會排列在工作佇列中。
  * 您可能已經有 5 個工作執行中，而每個工作都提交了 50 個 AU，當您提交的第六個工作 (假設為 20 個 AU)，它會在工作佇列中等候，並且在 20 個 AU 可用時開始執行。


**每個帳戶的並行 U-SQL 作業最大數目︰**20。 

這是可以同時在您的帳戶中執行之工作的最大數目。 超過此值會導致將較新的工作自動加入佇列。

## <a name="adjust-the-adla-quota-limits-per-account"></a>調整每個帳戶的 ADLA 配額限制

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇您已建立的 ADLA 帳戶
3. 按一下 [屬性]
4. 調整 [平行處理原則] 和 [並行工作] 以符合您的需求。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>若要增加最大配額限制

1. 在 Azure 入口網站中開啟支援要求。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 將 [問題類型] 設為 [配額]
3. 選取您的 **[訂用帳戶]** \(請確定它不是「試用」的訂用帳戶)。
4. 選取 [Data Lake Analytics] 作為配額類型

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. 在 [問題] 刀鋒視窗中，請說明您所要增加的限制，以及您為何需要這個額外的容量的**詳細資料**。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 確認您的連絡資訊，並 [建立支援要求]。

Microsoft 會檢閱您的要求，並嘗試盡速符合您的業務需求。

## <a name="next-steps"></a>後續步驟
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

