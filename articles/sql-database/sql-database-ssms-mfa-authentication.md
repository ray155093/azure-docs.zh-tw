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
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 2a2ee58ba86fef089401521d727a301f8b59ce75
ms.lasthandoff: 04/07/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>SQL Database 和 SQL 資料倉儲的通用驗證 (MFA 的 SSMS 支援)
Azure SQL Database 和 Azure SQL 資料倉儲支援使用「Active Directory 通用驗證」 ，從 SQL Server Management Studio (SSMS) 連線。 

- Active Directory 通用驗證支援兩種非互動式驗證方法 (Active Directory 密碼驗證和 Active Directory 整合式驗證)。 非互動式 Active Directory 密碼和 Active Directory 整合式驗證方法，可以在許多不同的應用程式 (ADO.NET、JDBC、ODBC 等) 中使用。 這兩種方法絕對不會產生快顯對話方塊。

- Active Directory 通用驗證也是支援 Azure Multi-Factor Authentication  (MFA) 的互動式方法。 Azure MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 搭配 Azure AD 使用互動式 MFA 時，會出現快顯對話方塊以進行驗證。

如需 Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)。
如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 網域名稱或租用戶 ID 參數   

從 [SSMS 第 17 版](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始，從其他 Azure Active Directory 匯入目前 Active Directory 的使用者，可以在連線時提供 Azure AD 網域名稱或租用戶 ID。 這可讓 **Active Directory 通用驗證**識別正確的驗證授權單位。 支援 Microsoft 帳戶 (MSA)，如 outlook.com、hotmail.com 或 live.com 時也需要此選項。 所有要使用通用驗證來進行驗證的這些使用者，皆必須輸入他們的 Azure AD 網域名稱或租用戶 ID。 此參數代表目前與 Azure 伺服器連結的 Azure AD 網域名稱/租用戶 ID。 例如，如果 Azure 伺服器與 Azure AD 網域 `contosotest.onmicrosoft.com` 相關聯，其中託管的使用者 `joe@contosodev.onmicrosoft.com` 是從 Azure AD 網域 `contosodev.onmicrosoft.com` 匯入，則用於驗證此使用者的必要網域名稱為 `contosotest.onmicrosoft.com`。 如果使用者是 Azure AD (與 Azure 伺服器連結) 的原生使用者，不是 MSA 帳戶，則不需要網域名稱或租用戶 ID。 若要輸入參數 (從 SSMS 第 17 版開始)，請在 [與資料庫連線] 對話方塊中完成對話方塊，並選取 [Active Directory 通用驗證]，然後按一下 [選項]，再按一下 [連線屬性] 索引標籤，核取 [AD 網域名稱或租用戶 ID] 方塊並提供驗證授權單位，如網域名稱 (**contosotest.onmicrosoft.com**) 或租用戶 ID 的 GUID。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>適用於 SQL Database 和 SQL 資料倉儲的通用驗證限制
* SSMS 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
* 只有單一 Azure Active Directory 帳戶可以使用通用驗證登入使用 SSMS 的執行個體。 若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 執行個體 (這項限制僅限於 Active Directory 通用驗證；您可以使用 Active Directory 密碼驗證、Active Directory 整合式驗證或 SQL Server 驗證來登入不同的伺服器)。
* SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
* DacFx 和 Schema Designer 均不支援通用驗證。
* 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。


## <a name="next-steps"></a>後續步驟

* 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。
* 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](sql-database-manage-logins.md)  
確定其他人可透過防火牆連線：[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)



