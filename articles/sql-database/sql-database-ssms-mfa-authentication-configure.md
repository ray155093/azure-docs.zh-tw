---
title: "設定 Multi-Factor Authentication - Azure SQL | Microsoft Docs"
description: "針對 SQL Database 和 SQL 資料倉儲，搭配使用 Multi-Factor Authentication 與 SSMS。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: b134999d407195aaf44babb3e4862b96cc1dc1ed
ms.openlocfilehash: b36f0cf8cbf0dfb310d6dd534906ee5391ce4cd5
ms.lasthandoff: 03/02/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證

本主題說明如何設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證。 

如需 Azure SQL Database 多重要素驗證的概觀，請參閱[適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證概觀](sql-database-ssms-mfa-authentication.md)。

## <a name="configuration-steps"></a>組態步驟

1. **設定 Azure Active Directory** - 如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../active-directory/active-directory-aadconnect.md)、[將您自己的網域名稱新增至 Azure AD (英文)](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟 (英文)](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **設定 MFA** - 如需逐步指示，請參閱[設定 Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md)。 
3. **設定 SQL Database 或 SQL 資料倉儲進行 Azure AD 驗證** - 如需逐步指示，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md)。
4. **下載 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新的 SSMS (至少是 2016 年 8 月)。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用通用驗證搭配 SSMS 進行連線

下列步驟示範如何使用最新的 SSMS 連線至 SQL Database 或 SQL 資料倉儲。

1. 若要使用通用驗證進行連線，請在 [連線到伺服器] 對話方塊中，選取 [Active Directory 通用驗證]。

   ![1mfa-universal-connect][1]
2. 如同平常針對 SQL Database 和 SQL 資料倉儲所做的一樣，您必須按一下 [選項]，然後在 [選項] 對話方塊上指定資料庫。 然後按一下 [ **連接**]。
3. 當 [登入您的帳戶]  對話方塊顯示時，請提供您 Azure Active Directory 身分識別的帳戶和密碼。

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > 如果是使用不需要 MFA 的帳戶進行通用驗證，則您可以在此時連線。 對於需要 MFA 的使用者，請繼續執行下列步驟。
   > 
   > 
4. 可能會顯示兩個 MFA 設定對話方塊。 這個一次性作業是根據 MFA 系統管理員設定而定，因此可能是選擇性的。 對於已啟用 MFA 的網域，有時會預先定義這個步驟 (例如，網域會要求使用者使用智慧卡和 pin)。  

   ![3mfa-setup][3]
5. 第二個可能的一次性對話方塊可讓您選取驗證方法的詳細資料。 可能的選項是由您的系統管理員所設定。

   ![4mfa-verify-1][4]
6. Azure Active Directory 會傳送確認資訊給您。 當您收到驗證碼時，請將其輸入 [輸入驗證碼] 方塊，然後按一下 [登入]。

   ![5mfa-verify-2][5]

驗證完成時，SSMS 即會正常連線 (假設認證和防火牆存取有效)。

## <a name="next-steps"></a>後續步驟

* 如需 Azure SQL Database 多重要素驗證的概觀，請參閱[適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證概觀](sql-database-ssms-mfa-authentication.md)。
* 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](sql-database-manage-logins.md)  
確定其他人可透過防火牆連線：[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


