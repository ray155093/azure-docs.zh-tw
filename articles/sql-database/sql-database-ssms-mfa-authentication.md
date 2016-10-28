<properties
   pageTitle="適用於 Azure AD MFA 與 SQL Database 和 SQL 資料倉儲的 SSMS 支援 | Microsoft Azure"
   description="針對 SQL Database 和 SQL 資料倉儲，搭配使用 Multi-Factor Authentication 與 SSMS。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# 適用於 Azure AD MFA 與 SQL Database 和 SQL 資料倉儲的 SSMS 支援

Azure SQL Database 和 Azure SQL 資料倉儲現在支援使用「Active Directory 通用驗證」，從 SQL Server Management Studio (SSMS) 連線。Active Directory 通用驗證是支援 Azure Multi-Factor Authentication (MFA) 的互動式工作流程。Azure MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。如需 Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)。

SSMS 現在支援：

- 搭配 Azure AD 使用互動式 MFA，可能會進行快顯對話方塊驗證。
- 非互動式 Active Directory 密碼和 Active Directory 整合式驗證方法，可以在許多不同的應用程式 (ADO.NET、JDBC、ODBC 等) 中使用。這兩種方法絕對不會產生快顯對話方塊。

設定使用者帳戶來使用 MFA 時，互動式驗證工作流程會透過快顯對話方塊、使用智慧卡等方式來要求額外的使用者互動。設定使用者帳戶來使用 MFA 時，使用者必須選取要連線的 Azure 通用驗證。如果使用者帳戶不需要 MFA，使用者仍然可以使用另外兩個 Azure Active Directory 驗證選項。

## 適用於 SQL Database 和 SQL 資料倉儲的通用驗證限制

- SSMS 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
- 只有單一 Azure Active Directory 帳戶可以使用通用驗證登入使用 SSMS 的執行個體。若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 執行個體(這項限制僅限於 Active Directory 通用驗證；您可以使用 Active Directory 密碼驗證、Active Directory 整合式驗證或 SQL Server 驗證來登入不同的伺服器)。
- SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
- DacFx 和 Schema Designer 均不支援通用驗證。
- Active Directory 通用驗證不支援 MSA 帳戶。
- 針對從其他 Active Directory 匯入至目前 Active Directory 的使用者，SSMS 中不支援 Active Directory 通用驗證。
- 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。

## 組態步驟

實作 Multi-Factor Authentication 需要四個基本步驟。

1. **設定 Azure Active Directory** – 如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../active-directory/active-directory-aadconnect.md)、[將您自己的網域名稱新增至 Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理您的 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)及[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。

2. **設定 MFA** – 如需逐步指示，請參閱[設定 Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md)。

3. **設定 SQL Database 或 SQL 資料倉儲進行 Azure AD 驗證** – 如需逐步指示，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲](sql-database-aad-authentication.md)。

4. **下載 SSMS** – 在用戶端電腦上，從 [下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新的 SSMS (至少是 2016 年 8 月)。

## 使用通用驗證搭配 SSMS 進行連線

下列步驟示範如何使用最新的 SSMS 連線至 SQL Database 或 SQL 資料倉儲。

1. 若要使用通用驗證進行連線，請在 [連線到伺服器] 對話方塊中，選取 [Active Directory 通用驗證]。![1mfa-universal-connect][1]

2. 如同平常針對 SQL Database 和 SQL 資料倉儲所做的一樣，您必須按一下 [選項]，然後在 [選項] 對話方塊上指定資料庫。然後按一下 [**連接**]。
3. 當 [登入您的帳戶] 對話方塊顯示時，請提供您 Azure Active Directory 身分識別的帳戶和密碼。![2mfa-sign-in][2]

    > [AZURE.NOTE] 如果是使用不需要 MFA 的帳戶進行通用驗證，則您可以在此時連線。對於需要 MFA 的使用者，請繼續執行下列步驟。
 
4. 可能會顯示兩個 MFA 設定對話方塊。這個一次性作業是根據 MFA 系統管理員設定而定，因此可能是選擇性的。對於已啟用 MFA 的網域，有時會預先定義這個步驟 (例如，網域會要求使用者使用智慧卡和 pin)。![3mfa-setup][3]

5. 第二個可能的一次性對話方塊可讓您選取驗證方法的詳細資料。可能的選項是由您的系統管理員所設定。![4mfa-verify-1][4]
 
6. Azure Active Directory 會傳送確認資訊給您。當您收到驗證碼時，請將其輸入至 [輸入驗證碼] 方塊，然後按一下 [登入]。![5mfa-verify-2][5]

驗證完成時，SSMS 即會正常連線 (假設認證和防火牆存取有效)。

##後續步驟  

授與對資料庫的其他存取權︰[SQL Database 驗證和授權：授與存取](sql-database-manage-logins.md) 確定其他人可以透過防火牆連線︰[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

<!---HONumber=AcomDC_0817_2016-->