---
title: "為 Azure 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE) | Microsoft Docs"
description: "為 Azure 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE)"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: barbkess
editor: 
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: a8a2c05bb341869e6dd67a9d6872fba6a35ffabe


---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>為 Azure 上的 Stretch Database 啟用透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
> 
> 

透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。 資料庫加密金鑰是由內建伺服器憑證保護。 內建伺服器憑證對每個 Azure 伺服器都是唯一的。 Microsoft 至少每 90 天會自動替換這些憑證。 如需 TDE 的一般描述，請參閱 [透明資料加密 (TDE)]。

## <a name="enabling-encryption"></a>啟用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 啟用 TDE，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定]  按鈕
3. 選取 [透明資料加密]  選項 ![][1]
4. 選取 [開啟]**** 設定，然後選取 [儲存]****
   ![][2]

## <a name="disabling-encryption"></a>停用加密
若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 停用 TDE，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)
2. 在資料庫刀鋒視窗中，按一下 [設定]  按鈕
3. 選取 [透明資料加密]  選項
4. 選取 [關閉] 設定，然後選取 [儲存]

<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->



<!--HONumber=Dec16_HO2-->


