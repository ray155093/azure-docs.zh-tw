<properties
   pageTitle="使用 Azure Active Directory 驗證連接到 SQL 資料倉儲 | Microsoft Azure"
   description="了解如何使用 Azure Active Directory 驗證連接到 SQL 資料倉儲。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# 使用 Azure Active Directory 驗證連線到 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


Azure Active Directory 驗證是 Azure Active Directory (Azure AD) 中使用身分識別連接到 Microsoft Azure SQL 資料倉儲的機制。您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。中央識別碼管理提供單一位置以管理 SQL 資料倉儲使用者並簡化權限管理。

## 優點

包括以下優點：

- 它提供 SQL Server 驗證的替代方案。
- 協助停止跨資料庫伺服器使用過多的使用者身分識別。
- 允許在單一位置的密碼替換
- 客戶可以管理使用外部 (AAD) 群組的資料庫權限。
- 它可以藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
- Azure Active Directory 驗證會使用自主資料庫使用者，在資料庫層級驗證身分。
- Azure Active Directory 針對連線到 SQL 資料倉儲的應用程式支援權杖型驗證。

> [AZURE.IMPORTANT] Azure Active Directory 驗證是預覽功能，必須遵循授權合約 (例如，Enterprise 合約、Microsoft Azure 合約或Microsoft 線上訂用帳戶合約) 中的預覽條款，以及任何適用的 [Microsoft Azure 預覽版補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## 組態步驟

設定步驟包括以下設定和使用 Azure Active Directory 驗證的程序。

1. 建立和填入 Azure Active Directory
2. 選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
3. 建立 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員。
4. 設定用戶端電腦
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
6. 使用 Azure AD 身分識別連接到您的資料倉儲。

搭配 Azure SQL Database 及搭配 Azure SQL 資料倉儲使用 Azure Active Directory 驗證的主要差異，是您必須使用 SQL Server Data Tools (而非 SQL Server Management Studio) 來連接到 SQL 資料倉儲。SQL 資料倉儲至少需要 2016 年 4 月 (版本 14.0.60311.1) 的 SQL Server Data Tools for Visual Studio 2015。Azure Active Directory 使用者目前不會顯示在 SSDT 物件總管中。解決方法是在 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中檢視使用者。
  
## 後續步驟 
- 完成詳細的步驟。針對 Azure SQL Database 及針對 Azure SQL 資料倉儲設定並使用 Azure Active Directory 驗證的詳細步驟幾乎完全相同。請依照[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](../sql-database/sql-database-aad-authentication.md)主題中的詳細步驟操作。
- 建立自訂資料庫角色，並加入使用者至角色。然後授與角色細微的權限。如需詳細資訊，請參閱[資料庫引擎權限使用者入門](https://msdn.microsoft.com/library/mt667986.aspx)。

<!---HONumber=AcomDC_0601_2016-->