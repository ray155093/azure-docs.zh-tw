---
title: "Azure SQL Database 安全性方針和限制 | Microsoft Docs"
description: "了解與安全性相關的 Microsoft Azure SQL Database 方針和限制。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/18/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 19a90a61d3ec895610d048cea2b9ebe4785aa2f4


---
# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Azure SQL Database 安全性方針和限制
本主題描述與安全性相關的 Microsoft Azure SQL Database 方針和限制。 管理您的 Azure SQL Database 安全性時，請考慮下列幾點。

## <a name="access-to-the-virtual-master-database"></a>虛擬 master 資料庫的存取權
通常，只有系統管理員需要存取 master 資料庫。 對每個使用者資料庫的例行存取，應該透過每個資料庫中建立的非系統管理員的自主資料庫使用者來存取。 當您使用自主資料庫使用者時，您不需要在 master 資料庫中建立登入。 如需詳細資訊，請參閱 [自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

## <a name="firewall"></a>防火牆
SQL Server 防火牆的範圍是整個 Azure SQL Server，您通常是透過入口網站來設定此防火牆，且此防火牆應該只准許系統管理員所使用的 IP 位址。 若要建立可為每個資料庫開啟必要 IP 位址範圍的資料庫範圍防火牆規則，請連接至使用者資料庫，然後使用 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) Transact-SQL 陳述式。

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。 若要從您的電腦存取 SQL Database，請務必確認您的用戶端電腦防火牆允許 TCP 連接埠 1433 上的傳出 TCP 通訊。 如果其他應用程式不需要，請封鎖 TCP 通訊埠 1433 的傳入連線。 

連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。 連接埠號碼的範圍從 11000 到 11999。 如需 TCP 連接埠的詳細資訊，請參閱 [適用於 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。

## <a name="authentication"></a>驗證
盡可能使用 Active Directory 驗證 (整合式安全性)。 如需有關設定 AD 驗證的資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)，以及《SQL Server 線上叢書》中的[選擇驗證模式](https://msdn.microsoft.com/library/ms144284.aspx)。 

使用自主資料庫使用者以加強延展性。 如需詳細資訊，請參閱[自主的資料庫使用者 - 使資料庫可攜](https://msdn.microsoft.com/library/ff929188.aspx)、[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 及[自主資料庫](https://technet.microsoft.com/library/ff929071.aspx)。

資料庫引擎會關閉保持閒置超過 30 分鐘的連接。 必須重新登入才能使用連接。

持續作用中的 SQL Database 連接，需要至少每 10 個小時重新授權一次 (由資料庫引擎執行)。 資料庫引擎嘗試重新授權時，會使用原本送出的密碼，但不需要使用者輸入。 基於效能考量，當在 SQL Database 中重設密碼時，並不會重新驗證連線，即使因連線共用而重設連線時也一樣。 這點與內部部署 SQL Server 的行為不同。 如果連接經過初始授權後，密碼已經變更，則必須終止連接並使用新密碼建立新的連接。 具有 KILL DATABASE CONNECTION 權限的使用者可以使用 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 命令，明確地終止 SQL Database 的連線。

## <a name="logins-and-users"></a>登入和使用者
管理 SQL Database 的登入和使用者時，會有一些限制。

* 執行 ``CREATE/ALTER/DROP DATABASE`` 陳述式時，您必須連接到 **master** 資料庫。 - master 資料庫中與伺服器層級主體登入對應的資料庫使用者無法被更改或刪除。 
* 英文 (美國) 是伺服器層級主體登入的預設語言。
* 只有系統管理員 (伺服器層級的主體登入或 Azure AD 系統管理員) 及 **master** 資料庫中 **dbmanager** 資料庫角色的成員，才具備執行 ``CREATE DATABASE`` 和 ``DROP DATABASE`` 陳述式的權限。
* 執行 ``CREATE/ALTER/DROP LOGIN`` 陳述式時，您必須連接到 master 資料庫。 不過，不建議使用登入。 請改為使用自主資料庫使用者。
* 若要連接到使用者資料庫，您必須在連接字串中提供資料庫名稱。
* 只有伺服器層級的主體登入及 **master** 資料庫中 **loginmanager** 資料庫角色的成員，才具備執行 ``CREATE LOGIN``、``ALTER LOGIN`` 及 ``DROP LOGIN`` 陳述式的權限。
* 在 ADO.NET 應用程式中執行 ``CREATE/ALTER/DROP LOGIN`` 和 ``CREATE/ALTER/DROP DATABASE`` 陳述式時，不允許使用參數化的命令。 如需詳細資訊，請參閱 [命令和參數](https://msdn.microsoft.com/library/ms254953.aspx)。
* 執行 ``CREATE/ALTER/DROP DATABASE`` 和 ``CREATE/ALTER/DROP LOGIN`` 陳述式時，這其中每一個陳述式在 Transact-SQL 批次中都必須是唯一的陳述式。 否則便會發生錯誤。 例如，下列 Transact-SQL 會檢查資料庫是否存在。 如果資料庫存在，則會呼叫 ``DROP DATABASE`` 陳述式來移除資料庫。 因為 ``DROP DATABASE`` 陳述式不是批次中唯一的陳述式，所以執行下列 Transact-SQL 陳述式時會產生錯誤。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

* 搭配執行 ``CREATE USER`` 陳述式和 ``FOR/FROM LOGIN`` 選項時，它必須是 Transact-SQL 批次中唯一的陳述式。
* 搭配執行 ``ALTER USER`` 陳述式和 ``WITH LOGIN`` 選項時，它必須是 Transact-SQL 批次中唯一的陳述式。
* 若要 ``CREATE/ALTER/DROP``，使用者必須擁有資料庫的 ``ALTER ANY USER`` 權限。
* 當資料庫角色的擁有者嘗試對該資料庫角色新增或移除另一個資料庫使用者時，可能會發生下列錯誤：**使用者或角色 'Name' 並不存在於此資料庫中。** 因為擁有者看不到使用者，所以會發生此錯誤。 若要解決這個問題，請為角色擁有者授與使用者的 ``VIEW DEFINITION`` 權限。 

如需登入和使用者的詳細資訊，請參閱 [管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。

## <a name="see-also"></a>另請參閱
[Azure SQL Database 防火牆](sql-database-firewall-configure.md)

[作法：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)

[SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Nov16_HO3-->


