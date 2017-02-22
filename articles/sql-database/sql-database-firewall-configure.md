---
title: "SQL Database 防火牆規則概觀 | Microsoft Docs"
description: "了解如何以伺服器層級和資料庫層級防火牆規則設定 SQL Database 防火牆，以管理存取權。"
keywords: "資料庫防火牆"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 5b94c884c585824ab580f307f3a04e27c8536947
ms.openlocfilehash: 05c6580bd0cd7af55325361e85cce9654e0169d6


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Azure SQL Database 防火牆規則概觀 
> [!div class="op_single_selector"]
> * [概觀](sql-database-firewall-configure.md)
> * [Azure 入口網站](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL Database 為 Azure 和其他網際網路式應用程式提供關聯式資料庫服務。 為了協助保護您的資料，防火牆會防止對您的資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。

若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器和資料庫層級建立防火牆規則。

* **伺服器層級防火牆規則：** 這些規則可讓用戶端存取整個 Azure SQL Server，也就是相同邏輯伺服器內的所有資料庫。 這些規則會儲存在 **master** 資料庫。 使用入口網站或使用 Transact-SQL 陳述式即可設定伺服器層級防火牆規則。 若要使用 Azure 入口網站或 PowerShell 建立伺服器層級的防火牆規則，您必須是訂用帳戶擁有者或訂用帳戶參與者。 若要使用 Transact-SQL 建立伺服器層級的防火牆規則，您必須以伺服器層級的主體登入或 Azure Active Directory 系統管理員身分連接到 SQL 資料庫執行個體 (這表示具有 Azure 層級權限的使用者必須先建立伺服器層級的防火牆規則)。
* **資料庫層級防火牆規則：** 這些規則可讓用戶端存取 Azure SQL Database 伺服器內的個別資料庫。 您可以針對每個資料庫建立這些規則，它們會儲存在個別的資料庫中。 (您可以為**主要**資料庫建立資料庫層級防火牆規則。)這些規則能協助您限制相同邏輯伺服器內的某些 (安全) 資料庫存取。 僅可使用 Transact-SQL 陳述式來設定資料庫層級防火牆規則。

   > [!NOTE]
   > 如需示範如何使用資料庫層級防火牆的教學課程，請參閱 [SQL Database 教學課程︰SQL Server 驗證、登入和使用者帳戶、資料庫角色、權限、伺服器層級防火牆規則和資料庫層級防火牆規則](sql-database-control-access-sql-authentication-get-started.md)。
   >

**建議：** Microsoft 建議在可行時使用資料庫層級防火牆規則來增強安全性，並且讓您的資料庫更具有可攜性。 當您有多個資料庫具有相同存取需求，且不想花時間個別設定每個資料庫時，請對系統管理員使用伺服器層級的防火牆規則。

> [!Note]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](sql-database-geo-replication-security-config.md)。
>

## <a name="firewall-overview"></a>防火牆概觀
一開始，防火牆會封鎖對您的 Azure SQL Server 的所有 Transact-SQL 存取。 若要開始使用 Azure SQL Server，您必須移至 Azure 入口網站並指定一或多個伺服器層級防火牆規則，啟用您的 Azure SQL Server 的存取。 使用防火牆規則來指定允許網際網路的哪些 IP 位址範圍，以及 Azure 應用程式是否可以嘗試連接到 Azure SQL Server。

若只要選擇性地授與 Azure SQL 伺服器上其中一個資料庫的存取權，您必須為所需的資料庫建立資料庫層級規則。 請為資料庫防火牆規則指定一個 IP 位址範圍，該範圍需在伺服器層級防火牆規則中指定的範圍之外，並確認用戶端的 IP 位址在資料庫層級規則中指定的範圍之內。

來自網際網路和 Azure 的連線嘗試必須先通過防火牆，才能到達您的 Azure SQL Server 或 SQL Database，如下圖所示：

   ![圖解防火牆設定。][1]

## <a name="connecting-from-the-internet"></a>從網際網路連線

當電腦嘗試從網際網路連線到資料庫伺服器時，防火牆會先根據資料庫層級的防火牆規則，檢查連線所要求之資料庫的來源 IP 位址︰

* 如果要求的 IP 位址在資料庫層級防火牆規則中指定的其中一個範圍內，就會允許連線至包含規則的 SQL Database。
* 如果要求的 IP 位址不在資料庫層級防火牆規則中指定的其中一個範圍內，則會檢查伺服器層級防火牆規則。 如果要求的 IP 位址在伺服器層級防火牆規則中指定的其中一個範圍內，就會允許連線。 伺服器層級防火牆規則適用於 Azure SQL Server 上的所有 SQL Database。  
* 如果要求的 IP 位址不在任何資料庫層級或伺服器層級防火牆規則中指定的範圍內，連線要求會失敗。

> [!NOTE]
> 若要從您的本機電腦存取 Azure SQL Database，請確定您的網路和本機電腦上的防火牆允許 TCP 連接埠 1433 上的傳出通訊。
> 

## <a name="connecting-from-azure"></a>從 Azure 連線
若要允許應用程式從 Azure 連接到您的 Azure SQL Server，必須啟用 Azure 連接。 當 Azure 的應用程式嘗試連線到您的資料庫伺服器時，防火牆會確認是否允許 Azure 連線。 開始和結束位址等於 0.0.0.0 的防火牆設定表示允許這些連線。 如果不允許連線嘗試，要求就不會到達 Azure SQL Database 伺服器。

> [!IMPORTANT]
> 這個選項會設定防火牆，以允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 選取這個選項時，請確定您的登入和使用者權限會限制為只有授權的使用者才能存取。
> 

> [!NOTE]
>  如需詳細資訊，請參閱 **針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠** 的 [SQL Database：內部與外部](sql-database-develop-direct-route-ports-adonet-v12.md)
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>建立第一個伺服器層級防火牆規則
可以使用 [Azure 入口網站](https://portal.azure.com/) 或以程式設計方式使用 REST API 或 Azure PowerShell，建立第一個伺服器層級防火牆設定。 後續的伺服器層級防火牆規則可以使用這些方法，以及透過 Transact-SQL 來建立和管理。 為了改進效能，系統會暫時在資料庫層級快取伺服器層級防火牆規則。 若應重新整理快取，請參閱 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。 如需伺服器層級防火牆規則的詳細資訊，請參閱 [做法：使用 Azure 入口網站設定 Azure SQL 伺服器防火牆](sql-database-configure-firewall-settings.md)。

## <a name="creating-database-level-firewall-rules"></a>建立資料庫層級防火牆規則
設定第一個伺服器層級防火牆之後，您可能想要限制對特定資料庫的存取。 如果您在資料庫層級防火牆規則中指定的 IP 位址範圍是在伺服器層級防火牆規則中指定的範圍之外，只有具有資料庫層級範圍中的 IP 位址的那些用戶端才可以存取資料庫。 對於資料庫，您最多可以有 128 個資料庫層級防火牆規則。 master 和使用者資料庫的資料庫層級防火牆規則可以透過 Transact-SQL 來建立和管理。 如需設定資料庫層級防火牆規則的詳細資訊，請參閱 [sp_set_database_firewall_rule (Azure SQL Databases)](https://msdn.microsoft.com/library/dn270010.aspx)。

## <a name="programmatically-managing-firewall-rules"></a>以程式設計方式管理防火牆規則
除了 Azure 入口網站之外，防火牆規則還可以程式設計方式使用 Transact-SQL、REST API 及 Azure PowerShell 來管理。 下表描述每個方法可用的命令集。

### <a name="transact-sql"></a>Transact-SQL
| 目錄檢視或預存程序 | 等級 | 說明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |伺服器 |顯示目前的伺服器層級防火牆規則 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |伺服器 |建立或更新伺服器層級防火牆規則 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |伺服器 |移除伺服器層級防火牆規則 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |資料庫 |顯示目前的伺服器層級防火牆規則 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |資料庫 |建立或更新伺服器層級防火牆規則 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |資料庫 |移除資料庫層級防火牆規則 |

### <a name="rest-api"></a>REST API
| API | 等級 | 說明 |
| --- | --- | --- |
| [列出防火牆規則](https://msdn.microsoft.com/library/azure/dn505715.aspx) |伺服器 |顯示目前的伺服器層級防火牆規則 |
| [建立防火牆規則](https://msdn.microsoft.com/library/azure/dn505712.aspx) |伺服器 |建立或更新伺服器層級防火牆規則 |
| [設定防火牆規則](https://msdn.microsoft.com/library/azure/dn505707.aspx) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [刪除防火牆規則](https://msdn.microsoft.com/library/azure/dn505706.aspx) |伺服器 |移除伺服器層級防火牆規則 |

### <a name="azure-powershell"></a>Azure PowerShell
| Cmdlet | 等級 | 說明 |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |伺服器 |返回目前的伺服器層級防火牆規則 |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |伺服器 |建立新的伺服器層級防火牆規則 |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |伺服器 |移除伺服器層級防火牆規則 |

> [!NOTE]
> 防火牆設定的變更最長可能延遲五分鐘才會生效。
> 
> 

## <a name="troubleshooting-the-database-firewall"></a>疑難排解資料庫防火牆
當對於 Microsoft Azure SQL Database 服務的存取未如預期運作時，請考慮下列幾點：

* **本機防火牆組態：** 在您的電腦可以存取 Azure SQL Database 之前，您可能需要在電腦上為 TCP 連接埠 1433 建立防火牆例外狀況。 如果您是在 Azure 雲端界限內建立連接，您可能必須開啟其他連接埠。 如需詳細資訊，請參閱 **針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠** 的 [SQL Database V12：內部與外部](sql-database-develop-direct-route-ports-adonet-v12.md)一節。
* **網路位址轉譯 (NAT)：** 由於 NAT，您的電腦用來連接到 Azure SQL Database 的 IP 位址，可能會不同於您電腦 IP 組態設定中顯示的 IP 位址。 若要檢視電腦用來連接到 Azure 的 IP 位址，請登入入口網站，並在裝載您資料庫的伺服器上瀏覽至 [設定]  索引標籤。 在 [允許的 IP 位址] 區段底下，[目前的用戶端 IP 位址] 隨即顯示。 對 [允許的 IP 位址] 按一下 [新增]，以允許此電腦存取伺服器。
* **允許清單的變更尚未生效：** Azure SQL Database 防火牆組態變更可能會延遲最多&5; 分鐘才能生效。
* **登入未獲授權或使用不正確的密碼：** 如果 Azure SQL Database 伺服器上的登入沒有權限，或所使用的密碼不正確，與 Azure SQL Database 伺服器的連線就會遭到拒絕。 建立防火牆設定只會讓用戶端有機會嘗試連線至您的伺服器；每個用戶端必須提供必要的安全性認證。 如需準備登入的詳細資訊，請參閱「管理 Azure SQL Database 中的資料庫、登入和使用者」。
* **動態 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：
  
  * 要求您的網際網路服務提供者 (ISP) 將可以存取 Azure SQL Database 伺服器的 IP 位址範圍指派給您的用戶端電腦，然後將 IP 位址範圍新增為防火牆規則。
  * 改為針對您的用戶端電腦取得靜態 IP 位址，然後將 IP 位址新增為防火牆規則。

## <a name="next-steps"></a>後續步驟
如需建立伺服器層級和資料庫層級防火牆規則的做法文章，請參閱：

* [使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)
* [使用 T-SQL 設定 Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md)
* [使用 PowerShell 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md)
* [使用 REST API 設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md)

如需建立資料庫的教學課程，請參閱 [使用 Azure 入口網站在幾分鐘內建立 SQL Database](sql-database-get-started.md)。
如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱 [SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要了解如何瀏覽至資料庫，請參閱 [管理資料庫存取與登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

## <a name="additional-resources"></a>其他資源
* [保護您的資料庫](sql-database-security-overview.md)
* [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png



<!--HONumber=Feb17_HO2-->


