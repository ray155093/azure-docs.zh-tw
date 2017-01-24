---
title: "SQL Database 驗證和授權 | Microsoft Docs"
description: "深入了解 SQL Database 安全性管理，特別是如何透過伺服器層級主體帳戶管理資料庫存取與登入安全性。"
keywords: "sql 資料庫安全性, 資料庫安全性管理, 登入安全性, 資料庫安全性, 資料庫存取權"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 0a65a93f-d5dc-424b-a774-7ed62d996f8c
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/14/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a3c3aabc6b1817df3bacb98a769ff167036ef3e6
ms.openlocfilehash: d54c7c6160e3c51f34bf2c7ba2661ab1e8a51bfa


---
# <a name="controlling-and-granting-database-access"></a>控制和授與資料庫存取權

使用各種不同的機制，可以將存取權授予經過驗證的使用者。 

## <a name="unrestricted-administrative-accounts"></a>不受限制的系統管理帳戶
有兩個可能的系統管理帳戶具有不受限制的權限，可以存取虛擬 master 資料庫和所有使用者資料庫。 這些帳戶稱為伺服器層級主體帳戶。

### <a name="azure-sql-database-subscriber-account"></a>Azure SQL Database 訂戶帳戶
建立邏輯 SQL 執行個體時，SQL Database 會在 master 資料庫中建立單一登入帳戶。 此帳戶有時候稱為 SQL Database 訂閱者帳戶。 此帳戶會使用 SQL Server 驗證 (使用者名稱和密碼) 連接。 此帳戶是邏輯伺服器執行個體和附加至該執行個體之所有使用者資料庫的系統管理員。 無法限制訂戶帳戶的權限。 只有其中一個帳戶可以存在。

### <a name="azure-active-directory-administrator"></a>Azure Active Directory 系統管理員
一個 Azure Active Directory 個人或群組帳戶也可以設定為系統管理員。 選擇性地設定 Azure AD 系統管理員，但是如果您想要使用 Azure AD 帳戶連接到 SQL Database，則必須設定 Azure AD 系統管理員。 如需有關設定 Azure Active Directory 存取權的詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md)和[適用於與 SQL Database 和 SQL 資料倉儲搭配使用之 Azure AD MFA 的 SSMS 支援](sql-database-ssms-mfa-authentication.md)。

### <a name="configuring-the-firewall"></a>設定防火牆
已設定個別 IP 位址或範圍的伺服器層級防火牆時，「Azure SQL Database 訂閱者帳戶」和 Azure Active Directory 帳戶能夠連接到 master 資料庫和所有使用者資料庫。 透過 [Azure 入口網站](sql-database-configure-firewall-settings.md) 並使用 [PowerShell](sql-database-configure-firewall-settings-powershell.md) 或使用 [REST API](sql-database-configure-firewall-settings-rest.md) 即可設定初始伺服器層級防火牆。 建立連線之後，也可以藉由使用 [Transact-SQL](sql-database-configure-firewall-settings-tsql.md) 來設定其他伺服器層級防火牆規則。

### <a name="administrator-access-path"></a>系統管理員存取路徑
伺服器層級防火牆設定正確時，SQL Database 訂戶帳戶和 Azure Active Directory SQL Server 系統管理員可以使用用戶端工具 (例如 SQL Server Management Studio 或 SQL Server Data Tools) 來連接。 只有最新的工具會提供所有的功能。 下圖顯示兩個系統管理員帳戶的一般組態。

![系統管理員存取路徑](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

在伺服器層級防火牆中使用開啟的連接埠時，系統管理員可以連接到任何 SQL Database。

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 連接到資料庫
如需建立伺服器、資料庫、伺服器層級防火牆規則，以及使用 SQL Server Management Studio 來查詢資料庫的逐步解說，請參閱[透過 Azure 入口網站和 SQL Server Management Studio 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started.md)。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="additional-server-level-administrative-roles"></a>其他伺服器層級系統管理角色
除了先前所討論的伺服器層級系統管理角色以外，SQL Database 還在可新增使用者帳戶的虛擬 master 資料庫中提供兩種受限的系統管理角色，以授與建立資料庫或管理登入的權限。

### <a name="database-creators"></a>資料庫建立者
其中一個系統管理角色是 dbmanager 角色。 此角色的成員可以建立新的資料庫。 若要使用此角色，您可在 master 資料庫中建立使用者，然後將該使用者新增至 **dbmanager** 資料庫角色。 使用者可以是自主資料庫使用者，或依據虛擬 master 資料庫中的 SQL Server 登入的使用者。

1. 使用系統管理員帳戶，連接至虛擬 master 資料庫。
2. 選擇性步驟︰使用 [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) 陳述式來建立 SQL Server 驗證登入。 範例陳述式︰
   
   ```
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > 建立登入或自主資料庫使用者時，請使用強式密碼。 如需詳細資訊，請參閱 [增強式密碼](https://msdn.microsoft.com/library/ms161962.aspx)。
   > 
   > 
   
   為了改進效能，系統會暫時在資料庫層級快取登入 (伺服器層級主體)。 若要重新整理驗證快取，請參閱 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。
3. 在虛擬 master 資料庫中，藉由使用 [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) 陳述式來建立使用者。 使用者可以是 Azure Active Directory 驗證自主資料庫使用者 (如果您已針對 Azure AD 驗證設定您的環境)，或 SQL Server 驗證自主資料庫使用者，或根據 SQL Server 驗證登入 (在上一個步驟中建立) 的 SQL Server 驗證使用者。範例陳述式︰
   
   ```
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
   CREATE USER Tran WITH PASSWORD = '<strong_password>';
   CREATE USER Mary FROM LOGIN Mary; 
   ```
4. 藉由使用 **ALTER ROLE** 陳述式，將新的使用者新增至 [dbmanager](https://msdn.microsoft.com/library/ms189775.aspx) 資料庫角色。 範例陳述式︰
   
   ```
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > dbmanager 是虛擬 master 資料庫中的資料庫角色，因此您只可以將使用者新增至 dbmanager 角色。 您無法將伺服器層級登入新增至資料庫層級角色。
   > 
   > 
5. 必要時，設定伺服器層級防火牆以允許新的使用者連接。

現在使用者可以連接至虛擬 master 資料庫，而且可以建立新的資料庫。 建立資料庫的帳戶會成為資料庫的擁有者。

### <a name="login-managers"></a>登入管理員
另一個系統管理角色是登入管理員角色。 此角色的成員可以在 master 資料庫中建立新的登入。 如果您想要的話，可以完成相同的步驟 (建立登入和使用者，並將使用者新增至 **loginmanager** 角色)，讓使用者能夠在虛擬 master 資料庫中建立新的登入。 通常並不需要這麼做，因為 Microsoft 建議使用自主資料庫使用者，這會在資料庫層級進行驗證，而不是根據登入來使用使用者。 如需詳細資訊，請參閱 [自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。

## <a name="non-administrator-users"></a>非系統管理員的使用者
一般而言，非系統管理員帳戶不需要虛擬 master 資料庫的存取權。 請使用 [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) 陳述式，在資料庫層級建立自主資料庫使用者。 使用者可以是 Azure Active Directory 驗證自主資料庫使用者 (如果您已針對 Azure AD 驗證設定您的環境)，或 SQL Server 驗證自主資料庫使用者，或根據 SQL Server 驗證登入 (在上一個步驟中建立) 的 SQL Server 驗證使用者。如需詳細資訊，請參閱 [自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)。 

若要建立使用者，請連線到資料庫，然後執行類似以下範例的陳述式︰

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

一開始，只有其中一個系統管理員或資料庫擁有者可以建立使用者。 若要授權讓其他使用者建立新的使用者，請授與該選取的使用者 `ALTER ANY USER` 權限，例如使用下列陳述式︰

```
GRANT ALTER ANY USER TO Mary;
```

若要將資料庫的完整控制權提供給其他使用者，請使用 `ALTER ROLE` 陳述式，讓他們成為 **db_owner** 固定資料庫角色的成員。

> [!NOTE]
> 之所以要根據登入建立資料庫使用者，主要是為了應付有 SQL Server 驗證使用者需要存取多個資料庫的情況。 根據登入的使用者會繫結至登入，只會針對該登入維護一個密碼。 在個別資料庫中的自主資料庫使用者是個別的實體，而且各個都會維護它自己的密碼。 如果它們不會維護各自的密碼相同，會造成自主資料庫使用者的混淆。
> 
> 

### <a name="configuring-the-database-level-firewall"></a>設定資料庫層級防火牆規則
最佳做法是，非系統管理員的使用者應該只能透過防火牆來存取所使用的資料庫。 做法是不透過伺服器層級防火牆授權其 IP 位址來讓他們存取所有資料庫，而是改用 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) 陳述式來設定資料庫層級防火牆。 無法藉由使用入口網站設定資料庫層級防火牆。

### <a name="non-administrator-access-path"></a>非系統管理員存取路徑
當資料庫層級防火牆設定正確時，資料庫使用者可以使用如 SQL Server Management Studio 或 SQL Server Data Tools 之類的用戶端工具來連接。 只有最新的工具會提供所有的功能。 下圖顯示一個典型的非系統管理員存取途徑。

![非系統管理員存取路徑](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>群組和角色
有效率存取管理會使用指派給群組和角色的權限，而不是指派給個別使用者的權限。 

- 使用 Azure Active Directory 驗證時，將 Azure Active Directory 使用者放入 Azure Active Directory 群組中。 建立群組的自主資料庫使用者。 將一或多個資料庫使用者放入[資料庫角色](https://msdn.microsoft.com/library/ms189121)中，然後將[權限](https://msdn.microsoft.com/library/ms191291.aspx)指派給資料庫角色。

- 使用 SQL Server 驗證時，在資料庫中建立自主資料庫使用者。 將一或多個資料庫使用者放入[資料庫角色](https://msdn.microsoft.com/library/ms189121)中，然後將[權限](https://msdn.microsoft.com/library/ms191291.aspx)指派給資料庫角色。

資料庫角色可以是內建的角色，例如 **db_owner**、**db_ddladmin**、**db_datawriter**、**db_datareader**、**db_denydatawriter** 和 **db_denydatareader**。 **db_owner** 通常是用來將完整權限授與少數幾個使用者。 其他固定的資料庫角色適用於快速開發簡單的資料庫，但不建議用於大多數實際執行資料庫。 例如，**db_datareader** 固定資料庫角色可授與資料庫中每個資料表的讀取存取權，這通常並非絕對必要。 最好是使用 [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) 陳述式來建立您自己的使用者定義資料庫角色，並謹慎地授與每個角色在業務需求上所需的最小權限。 當使用者是多個角色的成員時，會集所有這些角色的權限在一身。

## <a name="permissions"></a>權限
有超過 100 個權限可在 SQL Database 中分別授與或拒絕。 這些權限有許多為巢狀。 例如，結構描述上的 `UPDATE` 權限包括該結構描述中每個資料表的 `UPDATE` 權限。 如同大多數的權限系統，拒絕權限會覆寫授與權限。 因為權限的巢狀本質和數目，可能需要仔細研究，設計適當的權限系統以便適當地保護您的資料庫。 請從[權限 (Database Engine)](https://msdn.microsoft.com/library/ms191291.aspx) 的權限清單開始著手，然後檢閱[海報大小的權限圖](http://go.microsoft.com/fwlink/?LinkId=229142)。


### <a name="considerations-and-restrictions"></a>考量和限制
在 SQL Database 中管理登入和使用者時，請考慮下列各項︰

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


## <a name="next-steps"></a>後續步驟

- 若要深入了解防火牆規則，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。
- 如需所有 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。
- 如需教學課程，請參閱 [SQL 安全性入門](sql-database-get-started-security.md)
- 如需檢視和預存程序的相關資訊，請參閱[建立檢視和預存程序](https://msdn.microsoft.com/library/ms365311.aspx)
- 如需授與資料庫物件存取權的相關資訊，請參閱[授與資料庫物件的存取權](https://msdn.microsoft.com/library/ms365327.aspx)




<!--HONumber=Jan17_HO1-->


