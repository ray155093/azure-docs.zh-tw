---
title: "使用 Azure 入口網站複製 Azure SQL Database | Microsoft Docs"
description: "建立 Azure SQL Database 的複本"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 5da07683a4ca9fe1751debc163a0a5a595b6f82b
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 入口網站複製 Azure SQL Database

下列步驟說明如何利用 [Azure 入口網站](https://portal.azure.com) ，將 SQL Database 複製到相同的伺服器或不同的伺服器。 

> [!NOTE]
> 您也可以使用 [PowerShell](sql-database-copy-powershell.md) 或 [TRANSACT-SQL](sql-database-copy-transact-sql.md) 複製 SQL 資料庫。
>

若要複製 SQL Database，您需要下列項目：

* Azure 訂用帳戶。 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]  ，然後再回來完成這篇文章。
* 要複製的 SQL Database。 如果沒有 SQL Database，請遵循本文中以下的步驟： [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

## <a name="copy-your-sql-database"></a>複製您的 SQL Database
針對您要複製的資料庫開啟 SQL Database 頁面：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [更多服務]  >  [SQL Database]，然後按一下想要的資料庫。
3. 在 [SQL Database] 頁面上，按一下 [複製] ：
   
   ![SQL Database](./media/sql-database-copy-portal/sql-database-copy.png)
4. 在 [複製]  頁面上，系統會提供預設的資料庫名稱。 視需要輸入不同的名稱 (伺服器上所有的資料庫都必須有一個唯一的名稱)。
5. 選取 [目標伺服器] 。 目標伺服器就是要建立資料庫副本的位置。 您可以將資料庫複製到相同的伺服器或不同的伺服器。 您可以建立伺服器，或從清單中選取現有的伺服器。 
6. 選取 [目標伺服器] 之後，將會啟用 [彈性集區] 和 [定價層] 選項。 如果伺服器有集區，您便可以將資料庫複製到該集區。
7. 按一下 [確定]  開始複製程序。
   
   ![SQL Database](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>監視複製作業的進度
* 開始複製之後，按一下入口網站通知以取得詳細資訊。
  
    ![通知][3]
  
    ![monitor][4]

## <a name="verify-the-database-is-live-on-the-server"></a>確認伺服器上的資料庫為線上狀態
* 按一下 [更多服務]  >  [SQL Database]，然後確認新資料庫為 [線上] 狀態。

## <a name="resolve-logins"></a>解析登入
若要在複製作業完成之後解析登入，請參閱 [解析登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>後續步驟
* 若要了解將資料庫複製到不同的邏輯伺服器時如何管理使用者與登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。
* 若要使用 Azure 入口網站將資料庫匯出至 BACPAC 檔案，請參閱[使用 Azure 入口網站將資料庫匯出至 BACPAC](sql-database-export-portal.md)。
* [業務續航力概觀](sql-database-business-continuity.md)
* [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


