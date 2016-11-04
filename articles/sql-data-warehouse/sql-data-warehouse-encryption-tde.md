---
title: SQL 資料倉儲中的透明資料加密 (入口網站) | Microsoft Docs
description: SQL 資料倉儲中的透明資料加密 (TDE)
services: sql-data-warehouse
documentationcenter: ''
author: ronortloff
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: rortloff;barbkess;sonyama

---
# 開始使用 SQL 資料倉儲中的透明資料加密 (TDE)
> [!div class="op_single_selector"]
> * [安全性概觀](sql-data-warehouse-overview-manage-security.md)
> * [驗證](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## 需要的權限
您必須是系統管理員或 dbmanager 角色的成員，才能啟用透明資料加密 (TDE)。

## 啟用加密
若要啟用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀鋒視窗中，按一下 [設定] 按鈕
3. 選取 [透明資料加密] 選項 ![][1]
4. 選取 [開啟] 設定 ![][2]
5. 選取 [儲存] ![][3]

## 停用加密
若要停用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀鋒視窗中，按一下 [設定] 按鈕
3. 選取 [透明資料加密] 選項 ![][1]
4. 選取 [關閉] 設定 ![][4]
5. 選取 [儲存] ![][5]

## 加密 DMV
可以使用下列 DMW 來確認加密：

* [sys.databases]
* [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->