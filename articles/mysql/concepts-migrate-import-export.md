---
title: "適用於 MySQL 的 Azure 資料庫中的匯入與匯出 | Microsoft Docs"
description: "介紹如何在適用於 MySQL 的 Azure 資料庫中匯入與匯出資料庫"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>使用匯入和匯出移轉您的 MySQL 資料庫
本教學課程將說明在 Azure MySQL 資料庫中，使用 MySQL Workbench 匯入和匯出資料的最常見方式。 

## <a name="before-you-begin"></a>開始之前
若要逐步執行本作法指南，您需要：
- 適用於 MySQL 的 Azure 資料庫伺服器：[設計您的第一個 Azure MySQL 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench：[MySQL Workbench 下載 (英文)](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>使用一般工具
使用一般工具 (例如 MySQL Workbench、Toad 或 Navicat) 從遠端連接，然後在適用於 MySQL 的 Azure 資料庫中匯入或匯出資料。 在具有網際網路連接的用戶端電腦上使用這類工具，來連接到適用於 MySQL 的 Azure 資料庫。 如需使用 SSL 加密連接的最佳安全性作法，請參閱[在適用於 MySQL 的 Azure 資料庫中設定 SSL 連線能力](concepts-ssl-connection-security.md)。 在移轉到適用於 MySQL 的 Azure 資料庫時，您不需要將匯入和匯出檔案移至任何特定的雲端位置。 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>在目標適用於 MySQL 的 Azure 資料庫服務上建立資料庫
您必須在目標適用於 MySQL 的 Azure 資料庫伺服器上建立空的資料庫，您想要在該伺服器上使用 MySQL Workbench、Toad、Navicat 或任何適用於 MySQL 的協力廠商工具來移轉資料。 資料庫名稱可以與包含傾印資料的資料庫名稱相同，或者您可以建立名稱不同的資料庫。

![適用於 MySQL 的 Azure 資料庫的連接字串](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 連接字串](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>使用 MySQL Workbench 匯入和匯出
有兩種方式可在 MySQL Workbench 中匯出和匯入資料，每一種適用於不同用途。 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>使用物件瀏覽器內容功能表的資料表資料匯出與匯入精靈
![使用物件瀏覽器內容功能表的 MySQL Workbench 匯入匯出](./media/concepts-migrate-import-export/p1.png)

此精靈支援使用 CSV 和 JSON 檔案的匯入和匯出作業，且包含數個設定選項 (分隔符號、資料行選取、編碼選取等)。 此精靈可以針對本機或遠端連接的 MySQL 伺服器執行，而匯入動作包含資料表、資料行及類型對應。 您可以在資料表上按一下滑鼠右鍵，然後選擇 [資料表資料匯出精靈] 或 [資料表資料匯入精靈]，從物件瀏覽器的內容功能表中存取此精靈。 

## <a name="table-data-export-wizard"></a>資料表資料匯出精靈
下列範例會將資料表匯出至 CSV 檔案。 
- 以滑鼠右鍵按一下要匯出之資料庫的資料表。 
- 選取 [資料表資料匯出精靈]。 選取要匯出的資料行、資料列位移 (如果有的話)、 計數 (如果有的話)。 
- 在 [選取要匯出的資料] 視窗上，按 [下一步]。 選取檔案路徑、CSV 或 JSON 檔案類型、行分隔符號、括住的字串，以及欄位分隔符號。 
- 在 [選取輸出檔案位置] 視窗上選取 [下一步]，然後在 [匯出資料] 視窗上選取 [下一步]。


## <a name="table-data-import-wizard"></a>資料表資料匯入精靈
下列範例會從 CSV 檔案匯入資料表。
- 以滑鼠右鍵按一下要匯入之資料庫的資料表。 
- 瀏覽並選取要匯入的 CSV 檔案，然後選取 [下一步] 按鈕。 
- 選取目的地資料表 (新的或現有的)、選取或取消選取 [匯入前截斷資料表] 核取方塊，然後按 [下一步] 按鈕。
- 選取編碼方式和要匯入的資料行，然後選取 [下一步] 按鈕。 
- 在 [匯入資料] 視窗上選取 [下一步]，而它將據以匯入資料。

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>從管理導覽器存取 SQL 資料匯出和匯入精靈
使用此精靈，匯出或匯入從 MySQL Workbench 或使用 mysqldump 命令產生的 SQL。 從 [導覽器] 面板存取這些精靈，或是從主功能表選取 [伺服器]，然後選取 [資料匯入] 或 [資料匯出] 來存取。 

## <a name="data-export"></a>資料匯出
![使用管理導覽器的 MySQL Workbench 資料匯出](./media/concepts-migrate-import-export/p2.png)

此索引標籤可讓您匯出 MySQL 資料。 
- 選取每個您想要匯出的結構描述、選擇性地從每個結構描述中選取特定的結構描述物件/資料表，然後產生匯出。 設定選項包含匯出到專案資料夾或自封式 SQL 檔案、選擇性傾印儲存的常式和事件，或略過資料表資料。 
- 或者，在SQL 編輯器中，使用 [匯出結果集]，將特定的結果集匯出為另一種格式，例如 CSV、JSON、HTML 和 XML。 
- 選取要匯出的資料庫物件，並設定相關選項。 
- 按一下 [重新整理] 以載入目前的物件。 
- (選擇性) 開啟 [進階選項] 索引標籤，讓您能夠調整匯出作業。 例如，新增資料表鎖定、使用 replace 而不是 insert 陳述式、使用反引號字元將識別項括起來，以及其他更多作業。 
- 按一下 [開始匯出] 開始匯出程序。 


## <a name="data-import"></a>資料匯入
![使用管理導覽器的 MySQL Workbench 資料匯入](./media/concepts-migrate-import-export/p3.png)

此索引標籤允許從 [資料匯入] 作業匯入或還原匯出的資料，或是從來自 mysqldump 命令的其他匯出資料加以匯入或還原。 
- 選擇專案資料夾或自封式 SQL 檔案、選擇將匯入資料的結構描述，或選擇 [新增] 來定義新的結構描述。 
- 按一下 [開始匯入] 開始匯入程序，而它將據以匯入。

## <a name="next-steps"></a>後續步驟
如果您不熟悉如何開始使用此資料庫服務，請參閱：
-  [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)

