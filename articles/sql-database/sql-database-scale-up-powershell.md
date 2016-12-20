---
title: "使用 PowerShell 變更 Azure SQL Database 的服務層級和效能等級 | Microsoft Docs"
description: "變更 Azure SQL Database 的服務層級和效能等級說明如何使用 PowerShell 相應增加或減少您的 SQL Database。 使用 PowerShell 變更 Azure SQL Database 的定價層。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aea3bcbd6ac73a05b00b7b79b2dc47bf06d67f6f


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>使用 PowerShell 變更 SQL Database 的服務層級級和效能等級 (定價層)
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)
> 
> 

服務層和效能層級描述您的 SQL Database 可用的功能和資源，而且可以視您的應用程式變更進行更新。 如需詳細資訊，請參閱 [服務層](sql-database-service-tiers.md)。

請注意，變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 這個時間範圍會有不同，但平均在 4 秒下，而且超過 99% 的案例少於 30 秒。 很少發生，尤其是如果在連接停用時有大型交易執行中，則此時間範圍可能會更長。  

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在 6 小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，它應該在 3 小時內完成。

* 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
* 升級資料庫時若將 [異地複寫](sql-database-geo-replication-portal.md) 啟用，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫。
* 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。 您可以遵循 [從中斷情況復原](sql-database-disaster-recovery.md) 主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。
* 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](sql-database-business-continuity.md)。
* 完成變更之前，不會將新屬性套用至資料庫。

**若要完成本文，您需要下列項目：**

* Azure 訂用帳戶。 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費帳戶]  ，然後再回來完成這篇文章。
* Azure SQL Database。 如果您沒有 SQL Database，請遵循此文章中的步驟來建立： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
* Azure PowerShell。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>變更您的 SQL Database 的服務層級和效能等級
執行 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) Cmdlet 並將 **-RequestedServiceObjectiveName** 設為想要之定價層的效能等級；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*...

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>變更您的 SQL Database 之服務層級和效能等級的範例 PowerShell 指令碼
以您的值取代 ```{variables}``` (請勿包含大括號)。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>後續步驟
* [相應放大和縮小](sql-database-elastic-scale-get-started.md)
* [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
* [匯出 Azure SQL Database](sql-database-export-powershell.md)

## <a name="additional-resources"></a>其他資源
* [業務續航力概觀](sql-database-business-continuity.md)
* [SQL Database 文件](http://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database Cmdlet](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Nov16_HO3-->


