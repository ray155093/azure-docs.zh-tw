---
title: "疑難排解 Azure SQL Database 常見的連接問題"
description: "找出並解決 Azure SQL Database 常見之連接錯誤的步驟。"
services: sql-database
documentationcenter: 
author: dalechen
manager: felixwu
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 48ccd940efb75427461c3a8018aa6b31f46a626e


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>針對 Azure SQL Database 連線問題進行疑難排解
連線到 Azure SQL Database 失敗時，您會收到 [錯誤訊息](sql-database-develop-error-messages.md)。 本文是集中式主題，可協助您針對 Azure SQL Database 連線問題進行疑難排解。 本文除了介紹連線問題的[常見原因](#cause)，還推薦可協助您識別問題的[疑難排解工具](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)，以及提供疑難排解步驟來解決[暫時性錯誤](#troubleshoot-transient-errors)和[持續性或非暫時性錯誤](#troubleshoot-the-persistent-errors)。 最後，則是列出 [Azure SQL Database 連線問題的所有相關文章](#all-topics-for-azure-sql-database-connection-problems)。

如果您遇到連線問題，請嘗試本文中所述的疑難排解步驟。
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>原因
連線問題可能由下列任何一個原因所造成：

* 在設計應用程式的過程中，無法套用最佳作法和設計指南。  請參閱 [SQL Database 開發概觀](sql-database-develop-overview.md) 以便開始使用。
* Azure SQL Database 重新設定
* 防火牆設定
* 連線逾時
* 不正確的登入資訊
* 部分 Azure SQL Database 資源已達上限

一般而言，Azure SQL Database 的連線問題可以分類如下：

* [暫時性錯誤 (短期或間歇性)](#troubleshoot-transient-errors)
* [持續性或非暫時性錯誤 (定期重複發生的錯誤)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>嘗試使用 Azure SQL Database 連線問題疑難排解工具
如果您遇到特定的連線錯誤，請嘗試使用 [此工具](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)，此工具可協助您快速識別並解決您的問題。

## <a name="troubleshoot-transient-errors"></a>針對暫時性錯誤進行疑難排解
如果您的應用程式發生暫時性錯誤，請檢閱下列主題以取得有關如何疑難排解這些錯誤並減少其發生頻率的提示︰

* [疑難排解無法使用 伺服器 &lt;y&gt; 上的資料庫 &lt;x&gt; (錯誤：40613)](sql-database-troubleshoot-connection.md)
* [排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>針對持續性錯誤 (非暫時性錯誤) 進行疑難排解
如果應用程式持續無法連接到 Azure SQL Database，通常表示下列其中一項發生問題︰

* 防火牆組態。 Azure SQL Database 或用戶端防火牆封鎖 Azure SQL Database 的連接。
* 用戶端上的網路重新設定：例如，新的 IP 位址或 Proxy 伺服器。
* 使用者錯誤︰例如，輸入錯誤的連線參數，例如連接字串中的伺服器名稱。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解決永久性連線問題的步驟
1. 設定 [防火牆規則](sql-database-configure-firewall-settings.md) 允許用戶端 IP 位址。
2. 在用戶端與網際網路之間的所有防火牆上，請確定開放連接埠 1433 供輸出連線使用。 檢閱 [設定 Windows 防火牆以允許 SQL Server 存取](https://msdn.microsoft.com/library/cc646023.aspx) ，以取得其他指標。
3. 請確認您的連接字串和其他連線設定。 請參閱 [連線能力問題主題](sql-database-connectivity-issues.md#connections-to-azure-sql-database)中的「連接字串」一節。
4. 檢查儀表板中的服務健全狀況。 如果您認為沒有區域性停電，請參閱 [從中斷復原](sql-database-disaster-recovery.md) 以了解復原到新區域的步驟。

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>所有 Azure SQL Database 連線問題的主題
下表列出每一個直接適用於 Azure SQL Database 服務的連線問題主題。

| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 1 |[針對 Azure SQL Database 連線問題進行疑難排解](sql-database-troubleshoot-common-connection-issues.md) |這是針對 Azure SQL Database 中的連線問題進行疑難排解的登陸頁面。 說明如何識別並解決 Azure SQL Database 中的暫時性錯誤及持續性或非暫時性錯誤。 |
| 2 |[排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤](sql-database-connectivity-issues.md) |了解如何在 Azure SQL Database 中排解、診斷和防止 SQL 連接錯誤或暫時性錯誤。 |
| 3 |[一般暫時性錯誤處理指引](../best-practices-retry-general.md) |提供連線到 Azure SQL Database 時的暫時性錯誤處理指引。 |
| 4 |[針對 Microsoft Azure SQL Database 的連線問題進行疑難排解](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |此工具可協助識別您的問題並解決連線錯誤。 |
| 5 |[為「伺服器 &lt;y&gt; 上的資料庫 &lt;x&gt; 目前無法使用」的問題進行疑難排解。請稍後重試連接」錯誤](sql-database-troubleshoot-connection.md) |說明如何識別並解決 40613 錯誤：「目前無法使用伺服器 &lt;y&gt; 上的資料庫 &lt;x&gt;。 請稍後重試連接。」 |
| 6 |[SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md) |提供 SQL Database 用戶端應用程式的 SQL 錯誤碼相關資訊，例如常見的資料庫連線錯誤、資料庫複製問題，以及一般錯誤。 |
| 7 |[獨立資料庫的 Azure SQL Database 效能指引](sql-database-performance-guidance.md) |提供可協助您判斷哪個服務層是最適合您應用程式的指引。 同時也提供微調應用程式以充分運用您 Azure SQL Database 的建議。 |
| 8 |[SQL Database 開發概觀](sql-database-develop-overview.md) |提供各種技術之程式碼範例的連結，可用來連接到 Azure SQL Database 並與其互動。 |
| 9 |升級到 Azure SQL Database v12 頁面 ([Azure 入口網站](sql-database-upgrade-server-portal.md)、[PowerShell](sql-database-upgrade-server-powershell.md)) |提供使用 Azure 入口網站或 PowerShell 將現有 Azure SQL Database V11 伺服器和資料庫升級到 Azure SQL Database V12 的指示。 |

## <a name="next-steps"></a>後續步驟
* [針對 Azure SQL Database 效能問題進行疑難排解](sql-database-troubleshoot-performance.md)
* [針對 Azure SQL Database 權限問題進行疑難排解](sql-database-troubleshoot-permissions.md)
* [搜尋 Microsoft Azure 相關文件](http://azure.microsoft.com/search/documentation/)
* [檢視 Azure SQL Database 服務的最新更新](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>其他資源
* [SQL Database 開發概觀](sql-database-develop-overview.md)
* [一般暫時性錯誤處理指引](../best-practices-retry-general.md)
* [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)
* [使用 Azure SQL Database 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
* [使用彈性資料庫功能和工具的學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 




<!--HONumber=Dec16_HO2-->


