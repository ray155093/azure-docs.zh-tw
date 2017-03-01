---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: "針對 SQL Database 和 SQL 資料倉儲，搭配使用 Multi-Factor Authentication 與 SSMS。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>適用於 Azure AD MFA 與 SQL Database 和 SQL 資料倉儲的 SSMS 支援
Azure SQL Database 和 Azure SQL 資料倉儲現在支援使用「Active Directory 通用驗證」 ，從 SQL Server Management Studio (SSMS) 連線。 Active Directory 通用驗證是支援 Azure Multi-Factor Authentication  (MFA) 的互動式工作流程。 Azure MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 

* 如需 Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)。
* 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

## <a name="multi-factor-options"></a>多重要素選項

SSMS 現在支援：

* 搭配 Azure AD 使用互動式 MFA，可能會進行快顯對話方塊驗證。
* 非互動式 Active Directory 密碼和 Active Directory 整合式驗證方法，可以在許多不同的應用程式 (ADO.NET、JDBC、ODBC 等) 中使用。 這兩種方法絕對不會產生快顯對話方塊。

設定使用者帳戶來使用 MFA 時，互動式驗證工作流程會透過快顯對話方塊、使用智慧卡等方式來要求額外的使用者互動。設定使用者帳戶來使用 MFA 時，使用者必須選取要連線的 Azure 通用驗證。 如果使用者帳戶不需要 MFA，使用者仍然可以使用另外兩個 Azure Active Directory 驗證選項。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>適用於 SQL Database 和 SQL 資料倉儲的通用驗證限制
* SSMS 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
* 只有單一 Azure Active Directory 帳戶可以使用通用驗證登入使用 SSMS 的執行個體。 若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 執行個體 (這項限制僅限於 Active Directory 通用驗證；您可以使用 Active Directory 密碼驗證、Active Directory 整合式驗證或 SQL Server 驗證來登入不同的伺服器)。
* SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
* DacFx 和 Schema Designer 均不支援通用驗證。
* Active Directory 通用驗證不支援 MSA 帳戶。
* 針對從其他 Active Directory 匯入至目前 Active Directory 的使用者，SSMS 中不支援 Azure Active Directory 通用驗證。 不支援這些使用者，是因為它需要驗證帳戶的租用戶識別碼，但沒有提供識別碼的機制。
* 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。



## <a name="next-steps"></a>後續步驟

* 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。
* 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](sql-database-manage-logins.md)  
確定其他人可透過防火牆連線：[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)



